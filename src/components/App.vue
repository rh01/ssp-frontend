<template>
    <div>
        <navbar></navbar>
        <div class="container">
            <section class="section">
                <notification></notification>
                <router-view></router-view>
            </section>
        </div>
    </div>
</template>

<style>
    .notices .toast.is-success {
        background: #00822d
    }

    .field .help {
        width: 100%;
    }

    .field.has-addons {
        flex-wrap: wrap;
    }
</style>