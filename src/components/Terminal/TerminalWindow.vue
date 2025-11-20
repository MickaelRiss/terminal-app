<template>
  <div v-for="message in resultMessage" :key="message" class="result-message mb-2">
    <div v-if="Array.isArray(message[1])" class="result-message mb-2">
      <p class="text-secondary mb-2 d-flex align-items-center gap-3">
        Mickael 🤖 ~$
        <span class="text-white">{{ message[0] }} </span>
      </p>
      <ul>
        <li v-for="command in message[1]" :key="command.name" class="fw-bold">
          <div v-if="command.name">
            <span class="rc-color">
              {{ command.name }}
            </span>
            : {{ command.description }}
          </div>
          <span v-else class="fst-italic ">
            {{ command.description }}
          </span>
        </li>
      </ul>
    </div>
    <div v-else class="result-message">
      <p class="text-secondary mb-2 d-flex align-items-center gap-3">
        Mickael 🤖 ~$
        <span class="text-white">{{ message[0] }}</span>
      </p>
      <div>
        <div v-if="typeof message[1] === 'string' && isJSON(message[1])">
          <pre>{{ message[1] }}</pre>
        </div>
        <div v-else>
          {{ message[1] }}
        </div>
      </div>
    </div>
  </div>

  <div class="text-command d-flex align-items-center gap-3">
    <p class="text-secondary m-0">Mickael 🤖 ~$</p>
    <input type="text" v-model="userInput" ref="commandInput" @keydown.enter.prevent="handleEnter" @keydown.tab.prevent="handleTab">
  </div>
</template>

<script>
export default {
  name: 'TerminalWindow',

  data() {
    return {
      userInput: '',
      resultMessage: [],
      linkedin: 'https://www.linkedin.com/in/mickael-riss/',
      github: 'https://github.com/MickaelRiss',
      manCommand: [
        { name: 'clear', description: 'Delete all displayed content' },
        { name: 'mickael', description: "Display the main information about me"},
        { name: 'stack', description: "List my technical skills" },
        { name: 'cv', description: 'Download my CV' },
        { name: 'why', description: 'Display why you should hire me' },
        { name: 'email', description: 'If you liked this initiative, let me know by sending an email 😊' },
        { name: 'linkedin', description: 'Connect with me on LinkedIn' },
        { name: 'github', description: 'Check my GitHub profile for more projects' },
      ],
      stackCommand: [
        { name: 'Backend', description: "Python, Node.js and Ruby on Rails, Node.js." },
        { name: 'Frontend', description: 'Next.js, Vue.js, Bootstrap and Tailwind.' },
        { name: 'Database', description: "MySQL, PostgreSQL, MongoDB." },
      ],
      mickaelCommand: '{"first_name":"Mickael","last_name":"Riss","age":27,"email":"mickaelriss6@gmail.com","website":"www.mickael-riss.com","job":"Software Developer","city":"Currently in Montréal but moving back to Europe soon.."}',
      whyCommand: `
      {
        "Reasons_to_Hire_Mickael": [
          {
            "Technical_Skills": [
              "Strong experience with Python, JavaScript and the Next.js ecosystem.",
              "Excellent frontend integration skills: HTML, modern CSS and TailwindCSS.",
              "Practical backend experience with Ruby on Rails, Node.js, Prisma and PostgreSQL.",
              "Solid understanding of React and component-based development.",
              "Good knowledge of Git and modern development workflows."
            ]
          },
          {
            "Soft_Skills": [
              "Proactive and enjoys taking initiatives, completing tasks independently, and meeting deadlines.",
              "Effective communication skills, both verbally and in writing, with the ability to collaborate well in a team.",
              "Constant curiosity fuels a passion for discovering new things and tackling challenges as a self-learner, always ready to learn and expand skills.",
              "Fluent in French (native language) and in English.",
              "Thrives in a positive work environment and looks forward to bringing a cheerful spirit to your team! 😊"
            ]
          }
        ]
      }
      `,
      availableCommands: ['mickael', 'stack', 'cv', 'why', 'clear', 'coucou', 'man', 'email', 'linkedin', 'github'],
    };
  },

  methods: {
    handleDocumentClick(event) {
      if (this.$refs.commandInput && !this.$refs.commandInput.contains(event.target)) {
        this.$refs.commandInput.focus();
      }
    },

    isJSON(str) {
      try {
        JSON.parse(str);
        return true;
      } catch (e) {
        return false;
      }
    },

    downloadCV() {
      const link = document.createElement('a');
      link.href = '/CV_Mickael_Riss.pdf';
      link.target = '_blank';
      link.download = 'CV_Mickaël_Riss.pdf';
      link.click();
    },

    visitSocial(social) {
      const link = document.createElement('a');
      link.href = social;
      link.target = '_blank';
      link.click();
    },

    sendEmail() {
      const link = document.createElement('a');
      link.href = 'mailto:mickaelriss6@gmail';
      link.click();
    },

    handleEnter() {
      const command = this.userInput.toLowerCase().trim();
      const mickaelJson = JSON.stringify(JSON.parse(this.mickaelCommand), null, 2);
      const whyJson = JSON.stringify(JSON.parse(this.whyCommand), null, 2);

      switch (command) {
        case 'mickael':
          this.resultMessage.push(['mickael', mickaelJson]);
          break;
        case 'stack':
          this.resultMessage.push(['stack', this.stackCommand]);
          break;
        case 'social':
          this.resultMessage.push(['social', this.socialCommand]);
          break;
        case 'cv':
          this.downloadCV();
          this.resultMessage.push(['cv', 'Downloading CV... Done!']);
          break;
        case 'why':
          this.resultMessage.push(['why', whyJson]);
          break;
        case 'clear':
          this.resultMessage = [];
          break;
        case 'man':
          this.resultMessage.push(['man', this.manCommand]);
          break;
        case 'email':
          this.sendEmail();
          this.resultMessage.push(['email', 'Thanks for sending an email! I\'ll get back to you as soon as possible.']);
          break;
        case 'linkedin':
          this.visitSocial(this.linkedin);
          this.resultMessage.push(['linkedin', 'Visit my LinkedIn profile and let\'s connect!']);
          break;
        case 'github':
          this.visitSocial(this.github);
          this.resultMessage.push(['Github', 'Go check my GitHub profile and see what I\'ve done!']);
          break;
        default:
          this.resultMessage.push([this.userInput, `Command not found: '${this.userInput}'. You can type 'man' to get the list of commands.`]);
          break;
      }

      this.userInput = '';
    },

    handleTab(event) {
      event.preventDefault();
      const userInputLowerCase = this.userInput.toLowerCase();
      const matchingCommands = this.availableCommands.filter(command => command.startsWith(userInputLowerCase));

      if (matchingCommands.length === 1) {
        this.userInput = matchingCommands[0];
      }
    },
  },

  mounted() {
    this.$refs.commandInput.focus();
    document.addEventListener('click', this.handleDocumentClick);
  },
};
</script>

<style lang="scss" scoped>
  input {
    border: none;
    outline: none;
    background-color: transparent;
    color: #fff;
  }
</style>
