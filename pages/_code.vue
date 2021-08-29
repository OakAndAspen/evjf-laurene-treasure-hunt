<template>
    <div>
        <!-- Statut 0 - Les données ne sont pas encore chargées - On affiche un spinner de chargement -->
        <LoadingScreen v-if="status === 0"/>
        <!-- Statut 1 - L'énigme n'est pas encore résolue - On montre l'intro du poste, puis l'énigme,
        un champ pour entrer la réponse si nécessaire et un bouton -->
        <!-- Statut 2 - Le bouton a été pressé et est en cours de chargement - Le bouton montre un spinner -->
        <!-- Statut 3 - La réponse est fausse - Le bouton redevient normal, un message s'affiche juste au dessus -->
        <!-- Statut 4 - La réponse est juste - Toute disparaît, on affiche les infos de l'énigme, puis du poste -->
    </div>
</template>

<script>

export default {
    data() {
        let code = this.$route.params.code;

        return {
            status: 0
        }
    },
    async fetch() {
        let post = null;
        let enigma = null;

        post = await this.$content('posts', this.$route.params.code)
            .fetch()
            .catch(err => {
                if (process.client) {
                    window.location.href = "";
                }
            });
        if (!post) return;

        if (post.enigma) {
            enigma = await this.$content('enigmas', post.enigma)
                .fetch()
                .catch(err => {
                    if (process.client) {
                        window.location.href = "";
                    }
                });
        }

        return {
            status: 1,
            post,
            enigma
        }
    }
}
</script>
