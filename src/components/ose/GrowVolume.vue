<template>
    <div>
        <div class="hero is-light">
            <div class="hero-body">
                <div class="container">
                    <h1 class="title"><i class="material-icons">cloud_upload</i> Expand Persistent Volume</h1>
                </div>
                <h2 class="subtitle">
                    You can expand the persistent volume here</h2>
            </div>
        </div>
        <br>
        <b-message type="is-danger">
            Note that OpenShift will still show the original size after volume expansion. This is a display issue that currently cannot be fixed.
        </b-message>

        <form v-on:submit.prevent="growVolume">
            <cluster-select v-model="clusterid"></cluster-select>
            <b-field label="Persistent Volume Name">
                <b-input v-model.trim="pvName"
                         required>
                </b-input>
            </b-field>
            <b-message type="is-info">
                Not the name of the Persistent Volume Claim (PVC) itself, but the label written in the OpenShift console under "Storage" > column "Status" > <strong>fat</strong>
            </b-message>

            <b-field label="New Size">
                <b-input v-model.trim="newSize"
                         placeholder="100M"
                         required pattern="^[0-9]+[GM]$">
                </b-input>
            </b-field>
            <b-message type="is-info">
                The volume is being resized to the provided size. Shrinking is not possible.e.g 100M or 5G
            </b-message>

            <button v-bind:class="{'is-loading': loading}"
                    class="button is-primary">Expand persistent volume
            </button>
        </form>
    </div>
</template>

<script>
  import ClusterSelect from './ClusterSelect.vue'
  export default {
    components: {
      'cluster-select': ClusterSelect
    },
    data() {
      return {
        clusterid: '',
        project: '',
        pvName: '',
        newSize: '',
        loading: false
      };
    },
    methods: {
      growVolume: function() {
        this.loading = true;

        this.$http.post(this.$store.state.backendURL + '/api/ose/volume/grow', {
          clusterid: this.clusterid,
          project: this.project,
          newSize: this.newSize,
          pvName: this.pvName
        }).then(() => {
          this.loading = false;
        }, () => {
          this.loading = false;
        });
      }
    }
  };
</script>
