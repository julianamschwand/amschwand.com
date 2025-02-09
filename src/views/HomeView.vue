<script setup>
import { onMounted, ref } from 'vue'

const command = ref('')
const history = ref([])
const inputRef = ref(null)
const terminalRef = ref(null)
const executedCommands = ref([])
const welcomeMessage = ref("Welcome to: \n                     __                      __              \n ___ ___ _  ___ ____/ / _    _____ ____  ___/ /_______  __ _ \n/ _ `/  ' \\(_-</ __/ _ \\ |/|/ / _ `/ _ \\/ _  // __/ _ \\/  ' \\\n\\_,_/_/_/_/___/\\__/_//_/__,__/\\_,_/_//_/\\_,_(_)__/\\___/_/_/_/\n\nType 'help' for a list of available commands\n\n")

let historyIndex = 0

onMounted(() => {
    InputFocus()
})

const InputFocus = () => {
    setTimeout(() => {
        inputRef.value?.focus()
    }, 0) // Delay ensures Vue updates the DOM first
};

const ScrollToBottom = () => {
    setTimeout(() => {
        terminalRef.value?.scrollIntoView({ block: "end" })
    }, 0)
}

const HandleHistory = (event) =>{
    if (history.value.length > 0)
    {
        if (event.key === 'ArrowUp')
        {
            if (historyIndex === 0)
            {
                historyIndex = history.value.length
            }
            historyIndex--
            command.value = history.value[historyIndex]
        }
        else if (event.key === 'ArrowDown')
        {
            if (historyIndex === history.value.length)
            {
                return
            }
            historyIndex++
            command.value = history.value[historyIndex]
        }
    }
}

const HandleCommand = () => {
    let output = ''
    let commandSplit = command.value.trim().split(" ")

    switch (commandSplit[0]) {
        case 'help':
            output = "Available commands:\n\nhelp - shows available commands\nclear - clears the console\necho - writes back any text entered\nls - shows all available subdomains\ncsd - changes to a subdomain\npwd - prints the working domain\nwhoami - shows the current user\nexit - exit the website\n\n(+ some others)"
            break
        case 'clear':
            executedCommands.value = []
            welcomeMessage.value = ''
            break
        case 'echo':
            output = commandSplit.slice(1).join(" ")
            break
        case 'exit':
            window.location.href = 'about:blank'
            break
        case 'ls':
            output = 'Available subdomains:\n\ncloud - Nextcloud'
            break
        case 'whoami':
            output = 'user'
            break
        case 'sudo':
            output = "Permission denied ;)"
            break
        case 'csd':
            window.location.href = `https://${commandSplit[1]}.amschwand.com`
            break
        case 'pwd':
            output = 'amschwand.com'
            break
        case 'rick':
        case 'rickroll':
            window.location.href = 'https://www.youtube.com/watch?v=dQw4w9WgXcQ'
            break
        case '':
            output = ''
            break
        default:
            output = `${command.value}: command not found`
            break
    }

    history.value.push(command.value)
    if (commandSplit[0] !== 'clear') executedCommands.value.push({ command: command.value, output: output })

    command.value = ''
    historyIndex = history.value.length

    ScrollToBottom()
}
</script>

<template>
    <p id="welcome-message" v-if="welcomeMessage">{{ welcomeMessage }}</p>
    <div v-for="executedCommand in executedCommands">
        <div class="command-line" >
            <p><span style="color: #98c379">user@amschwand.com</span>:<span style="color: #61afef">/</span>$</p>
            <p class="executed-command">{{ executedCommand.command }}</p>
        </div>
        <p v-if="executedCommand.output" class="output">{{ executedCommand.output }}</p>
    </div>
    <div class="command-line">
        <p><span style="color: #98c379">user@amschwand.com</span>:<span style="color: #61afef">/</span>$</p>
        <input type="text" v-model="command" ref="inputRef" @keydown.enter="HandleCommand" @keydown.up="HandleHistory" @keydown.down="HandleHistory" @blur="InputFocus"/>
    </div>
    <div ref="terminalRef"></div>
</template>

<style scoped>
input {
    background: transparent;
    border: none;
    color: white;
    outline: none;
    font-family: monospace;
    font-size: 18px;
    width: 100%;
    flex-grow: 1;
    display: inline-block;
    margin-top: -1px;
    margin-bottom: 0px;
}
p {
    color: white;
    font-family: monospace;
    font-size: 18px;
    display: inline-block;
    margin-bottom: 0px;
    margin-top: 0px;
}
.command-line {
    display: flex;
    gap: 5px;
    width: 100%;
    white-space: nowrap;
}

.executed-command {
    margin-left: 2px;
}

#welcome-message, .output {
    white-space: pre-wrap;
}

</style>