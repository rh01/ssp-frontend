<template>
    <div>
        <div class="hero is-light">
            <div class="hero-body">
                <div class="container">
                    <h1 class="title"><i class="material-icons">account_circle</i> Create Docker Pull-Secret</h1>
                </div>
                <h2 class="subtitle">
                    Here you can allow your project to pull from a specific Docker repository.</h2>
            </div>
        </div>
        <br>
        <form v-on:submit.prevent="createPullSecret">
            <cluster-select v-model="clusterid"></cluster-select>
            <project-select v-bind:clusterid="clusterid" v-bind:project.sync="project"></project-select>

            <b-field label="Username">
                <b-input v-model.trim="username" required></b-input>
            </b-field>

            <b-field label="Password">
                <b-input v-model.trim="password" type="password" password-reveal required></b-input>
            </b-field>
            <b-message type="is-info">
                "Username" and "Password" from the email, that was sent after you created an Artifactory Docker-Repository. <a target="_blank" href="https://confluence.sbb.ch/x/jwf5Q">More infos</a>
	    </b-message>

            <button v-bind:class="{'is-loading': loading}"
                    class="button is-primary">Create secret
            </button>
        </form>
    </div>
</template>

<script>
  import ClusterSelect from './ClusterSelect.vue'
  import ProjectSelect from './ProjectSelect.vue'
  export default {
    components: {
      'cluster-select': ClusterSelect,
      'project-select': ProjectSelect
    },
    data() {
        return {
            clusterid: '',
            project: '',
            username: '',
            password: '',
            loading: false,
        };
    },
    methods: {
        createPullSecret: function () {
            this.loading = true;

            this.$http.post(this.$store.state.backendURL + '/api/ose/secret/pull', {
                clusterid: this.clusterid,
                project: this.project,
                username: this.username,
                password: this.password
            }).then(() => {
                this.loading = false;
            }, () => {
                this.loading = false;
            });
        }
    }
};
</script>
