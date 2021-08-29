<template>
    <div>
        <!-- Statut 0 - Les données ne sont pas encore chargées - On affiche un spinner de chargement -->
        <LoadingScreen v-if="status === 0"/>
        <!-- Statut 1 - L'énigme n'est pas encore résolue - On montre le contenu de l'énigme,
        un champ pour entrer la réponse si nécessaire et un bouton -->
        <div v-if="status > 0 && status < 3" class="container py-4">
            <h1 class="font-booter text-center my-4">{{ post.title }}</h1>
            <nuxt-content :document="enigma"/>
            <form v-if="enigma.answer">
                <input v-model="answer"
                       class="form-control form-control-lg my-4"
                       placeholder="Entrez la réponse ici...">
                <!-- Statut 2 - La réponse est fausse - Un message s'affiche -->
                <div v-if="status === 2" class="alert alert-danger">{{wrongAnswerMessages[wrongAnswerIndex]}}</div>
                <button class="btn btn-info w-100"
                        @click.stop.prevent="onSubmit()">
                    Envoyer
                </button>
            </form>
        </div>
        <!-- Statut 3 - La réponse est juste - Tout disparaît, on affiche le contenu du poste -->
        <div v-if="status === 3" class="container py-4">
            <h1 class="font-booter text-center my-4">Bonne réponse!</h1>
            <nuxt-content :document="post"/>
        </div>
    </div>
</template>

<script>

export default {
    data() {
        return {
            status: 0,
            post: null,
            enigma: null,
            answer: '',
            wrongAnswerIndex: 0,
            wrongAnswerMessages: [
                "Fausse réponse!",
                "Nope...",
                "Nan, c'est pas ça.",
                "WROOOOOOONG",
                "C'est pas juste, pas juste du tout!",
                "Faux, essayez encore!"
            ]
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
        console.log(post);

        if (post.enigma) {
            enigma = await this.$content('enigmas', post.enigma)
                .fetch()
                .catch(err => {
                    if (process.client) {
                        window.location.href = "";
                    }
                });
        }

        this.post = post;
        this.enigma = enigma;
        this.status = 1;
    },
    methods: {
        onSubmit() {
            let rightAnswer = this.enigma.answer.toString().toLowerCase().trim();
            let givenAnswer = this.answer.toString().toLowerCase().trim();
            if (rightAnswer === givenAnswer) this.status = 3;
            else {
                this.shuffleMessages();
                this.status = 2;
            }
        },
        shuffleMessages() {
            let index = 0;
            do {
                index = Math.floor(Math.random() * this.wrongAnswerMessages.length);
            } while (index === this.wrongAnswerIndex);

            this.wrongAnswerIndex = index;
        }
    }
}
</script>
