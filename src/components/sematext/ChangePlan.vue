<template>
    <div>
        <div class="hero is-light">
            <div class="hero-body">
                <div class="container">
                    <h1 class="title"><i class="material-icons">attach_money</i> Adjust Sematext Logsene Daily Volume and Limit.
		</h1>
                </div>
                <h2 class="subtitle">
                    You can adjust your MB-Limit for the Sematext Logsene app here.</h2>
            </div>
        </div>
        <br>
        <form v-on:submit.prevent="updateSematextPlanAndLimits">
            <b-field label="Logsene App">
                <b-select placeholder="Choose your app"
                          :loading="loading"
                          v-model="appId"
                          required>
                    <option
                            v-for="app in apps"
                            :value="app.appId"
                            :key="app.name">
                        {{ app.name }}, Aktueller Plan: {{ app.planName }}, {{ app.pricePerMonth }}$
                    </option>
                </b-select>
            </b-field>

            <b-message type="is-info">
                Sematext plans: <a href="https://confluence.sbb.ch/x/AisiMg">Costs Log-Management</a><br/>
                - The "Daily Volume" should be just below your average expected log amount<br/>
                - The "Daily Volume" (capacity) is cheaper, but is always billed, even if you do not use it
                - Your log usage should be between the "Daily Volume" and the "Daily Volume Limit"<br/>
                - Sematext will reject logs if the usage increases above the "Daily Volume Limit"<br/>
            </b-message>

            <b-field label="New Logsene Plan">
                <b-select placeholder="Choose your Sematext plan"
                          :loading="loading"
                          v-model="planId"
                          @input="updateLimit()"
                          required>
                    <option
                            v-for="plan in plans"
                            :value="plan.planId"
                            :key="plan.name">
                        {{ plan.name }}, {{ plan.pricePerMonth }}$, Limit: {{ plan.defaultDailyMaxLimitSizeMb }}MB
                    </option>
                </b-select>
            </b-field>

            <b-field label="Daily Volume Limit">
                <b-field>
                    <b-input v-model.trim="limit" required>
                    </b-input>
                    <p class="control">
                        <span class="button is-static">MB</span>
                    </p>
                </b-field>
            </b-field>

            <button v-bind:class="{'is-loading': loading}"
                    class="button is-primary">Save selected
            </button>
        </form>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                apps: [],
                plans: [],
                appId: '',
                planId: '',
                limit: '',
                loading: false
            };
        },
        mounted: function () {
            this.getUsersApps();
            this.getAppPlans();
        },
        methods: {
            updateLimit: function() {
                if (this.planId) {
                    this.plans.forEach(p => {
                        if (p.planId === this.planId) {
                            this.limit = p.defaultDailyMaxLimitSizeMb;
                        }
                    });
                }
            },
            getUsersApps: function () {
                this.loading = true;
                this.$http.get(this.$store.state.backendURL + '/api/sematext/logsene').then((res) => {
                    this.loading = false;
                    this.apps = res.body;
                }, () => {
                    this.loading = false;
                });
            },
            getAppPlans: function () {
                this.loading = true;
                this.$http.get(this.$store.state.backendURL + '/api/sematext/plans').then((res) => {
                    this.loading = false;
                    this.plans = res.body;
                }, () => {
                    this.loading = false;
                });
            },
            updateSematextPlanAndLimits: function () {
                this.loading = true;

                this.$http.post(this.$store.state.backendURL + '/api/sematext/logsene/' + this.appId + '/plan', {
                    planId: this.planId,
                    limit: parseInt(this.limit)
                }).then(() => {
                    this.loading = false;
                    this.getUsersApps();
                }, () => {
                    this.loading = false;
                });
            }
        }
    };
</script>
