<template>
    <div class="bg">
        <div id="text-buffer" class="text-buffer">
            <span v-html="buffer">{{ buffer }}</span><span v-if="showCursor" class="cursor">▋</span>
        </div>
    </div>
</template>
    

<style>

    .text-buffer {
       color: white;
       font-family: 'JetBrains Mono';
       white-space: pre;
       font-size: 16px;
       font-weight: 16px;
    } 
    
    .cursor{
        animation:blinkingText 1.2s infinite;
    }

    a {
        text-decoration: none;
        color: dodgerblue;
    }

    @keyframes blinkingText{
        0%{     color: rgb(255, 254, 254);    }
        49%{    color: rgb(255, 255, 255); }
        60%{    color: transparent; }
        99%{    color:transparent;  }
        100%{   color: rgb(255, 255, 255);    }
    }

</style>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
    name: "Terminal",
    components: {},
    data: function() {
        return {
            buffer: "",
            lines: ["\n"],
            line: 2,
            showCursor: true,
        }
    },
    mounted() { 

        this.boot();        
    },
    methods: {
        processCommand(command: string) {
            let parsed = command.split(" ");

            if (parsed[0] == "clear") {
                this.clear();
            } else if (parsed[0] == "echo") {
                let message = "";
                parsed.forEach( (cmd, i) => {
                    if (i > 0) {
                        message += cmd;
                    }
                });
                this.printLine(message);
            } else if (parsed[0] == "help") {
                this.printLine("LolzOS help page:");
                this.printLine("use `clear` to clear the console.");
                this.printLine("use `echo` [message] to print a string on the console.");
                this.printLine("use `about` [lolz | site] to get information about me (Lolz) or the website.");
                this.printLine("use `uname` to get the system version.");
                this.printLine("use `reboot` to reboot the system.");
            } else if(parsed[0] == "about") {
                this.about(parsed[1]);
            } else if(parsed[0] == "uname") {
                this.uname();
            } else if(parsed[0] == "reboot") {
                this.reboot();
            } else if (command != ""){
                this.printLine("Unkown command!");
            }
        }, 
        printLine(msg: string) {
            this.line += 1;
            this.lines[this.line] = msg;
            this.line += 1;
            this.updateBuffer();
        },

        uname() {
            this.printLine(`LolzOS ${process.env.VUE_APP_VERSION}`);
        },

        clear() {
            this.lines = ["\n"];
            this.line = 1;
            this.updateBuffer();
        },
        boot() {

            this.printLine("Booting LolzOS");

            this.updateBuffer();

            setTimeout(() => this.clear(), 1000);

            setTimeout(() => {

                this.lines = ["\n", "Type `help` for help.", "> "]
                this.line = 2;

                this.updateBuffer();

                window.addEventListener("keydown", e => {
                    if (e.code == "Enter") {

                        this.processCommand(this.lines[this.line].substring(this.lines[this.line].length, 2));

                        this.line += 1;
                        this.lines[this.line] = "> ";

                        this.updateBuffer();

                    } else if (e.code == "Backspace") {

                        if (this.lines[this.line].length > 2) {
                            this.lines[this.line] = this.lines[this.line].substring(0, this.lines[this.line].length - 1);

                            this.updateBuffer();
                        }
                    } else if (!e.ctrlKey) {
                        
                        let keycode = e.keyCode;

                        let valid = (keycode > 47 && keycode < 58) || keycode == 32 || (keycode > 64 && keycode < 91) || (keycode > 95 && keycode < 112)  || (keycode > 185 && keycode < 193) || (keycode > 218 && keycode < 223);

                        if (valid) {
                            this.lines[this.line] += e.key;

                            this.updateBuffer();
                        }
                    } 
                });

            }, 2000);
        },
        updateBuffer() {
            this.buffer = "";

            this.lines.forEach((line, i) => {
                if (i == this.lines.length - 1) {
                    this.buffer += line;
                } else {
                    this.buffer += (line + "\n");
                }
            }); 
        },
        
        reboot() {
            this.printLine("Rebooting...");
            setTimeout(() => location.reload(), 500);
        },

        about(parameter: string) {
            if (parameter == "lolz") {
                this.printLine("About LolzDEV:");
                this.printLine("Hello! Lolz speaking, my name's Lorenzo but everyone on the internet call me Lolz.");
                this.printLine("I live in Italy 🇮🇹 and I'm 15 years old, I like programming and technology in general, I also like motorbikes and OSes with a 🐧 as <em>mascotte</em>.");
            } else if (parameter == "site" || parameter == "lolzos") {
                this.printLine("This website has been created with the VueJS framework in TypeScript!");
                this.printLine("Since opensource is good and all, this website source code is available on <a target=\"_blank\" href=\"https://github.com/LolzDEV\">GitHub</a>.")
            } else {
                this.printLine("Usage: `about` [lolz | site]");
            }
        }
    }
})
</script>