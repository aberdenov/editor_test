<template>
    <div>
        <div class="btn_cont">
            <Button label="Run" @click="runCode" />
            <Dropdown
                v-model="selectedLanguage"
                :options="languages"
                optionLabel="label"
                optionValue="value"
                placeholder="Select Language" 
            />
        </div>
        <div>
            <MonacoEditor
                theme="vs"
                :options="options"
                :language="selectedLanguage"
                :height="400"
                :original="original"
                v-model:value="code"
            ></MonacoEditor>
        </div>
        <div>
            <h3>Console Output:</h3>
            <pre>{{ output }}</pre>
        </div>
    </div>
</template>

<script setup>
    import { ref, onMounted, onBeforeUnmount } from "vue"
    import MonacoEditor from 'monaco-editor-vue3';
    import Button from 'primevue/button' 
    import Dropdown from "primevue/dropdown"

    const languages = ref([
        { label: "Go", value: "go" },
        { label: "Python", value: "python" },
    ])
    const selectedLanguage = ref("go")
    const output = ref(null)
    const code = ref('')
    const isRunning = ref(false)

    const runCode = async () => {
        isRunning.value = true
        
        setTimeout(() => {
            fetchMockServer(code.value)
            .then((response) => {
                output.value = response
                isRunning.value = false
            })
            .catch((error) => {
                output.value = error
                isRunning.value = false
            })
        }, 500)
    }    

    const fetchMockServer = (code) => {
        return new Promise((resolve, reject) => {
            let result = ''
            
            if (!code) {
                result = {
                    "status": "error",
                    "output": "SyntaxError: Unexpected token"
                }

                reject(result)
            } else {
                result = {
                    "status": "success",
                    "output": code
                }

                resolve(result)
            }
        })
    }
</script>

<style>
    .btn_cont {
        display: flex;
        gap: 20px;
        margin-bottom: 20px;
    }
</style>