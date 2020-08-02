<template>
  <div class="container">
    <header>
      <div class="center">
        <h1>GENERADOR DE ARGUMENTOS</h1>
        <p>
          Obtén un argumento aleatorio. Solo haz click en "Generar". Si te gusta un resultado, puedes hacer click sobre éste para bloquearlo.
        </p>
        <div id="navbar" class="controls">
          <form>
            <select>
              <option value="">
                Cualquier género
              </option>
              <option v-for="genre in genres" :key="genre.id" value="genre.id">
                {{ genre.name }}
              </option>
            </select>
            <button @click.prevent="generate">
              <p>Generar</p>
              <RefreshIcon />
            </button>
          </form>
        </div>
      </div>
    </header>

    <main class="center">
      <section>
        <h2>PROTAGONISTA</h2>
        <p>
          <span :class="{ blocked: protagonist.blocked }" @click="toggleBlock('protagonist')">{{ protagonist.value }}</span> que <span :class="{ blocked: protagonistAbout.blocked }" @click="toggleBlock('protagonistAbout')">{{ protagonistAbout.value }}</span>.
        </p>
      </section>

      <section>
        <h2>PERSONAJE SECUNDARIO</h2>
        <p>
          <span :class="{ blocked: secondaryCharacter.blocked }" @click="toggleBlock('secondaryCharacter')">{{ secondaryCharacter.value }}</span> que <span :class="{ blocked: secondaryCharacterAbout.blocked }" @click="toggleBlock('secondaryCharacterAbout')">{{ secondaryCharacterAbout.value }}</span>.
        </p>
      </section>

      <section>
        <h2>SINOPSIS</h2>
        <p>
          Es <span :class="{ blocked: genre.blocked }" @click="toggleBlock('genre')">{{ genre.value }}</span> sobre <span :class="{ blocked: subject.blocked }" @click="toggleBlock('subject')">{{ subject.value }}</span>.
          Arranca <span :class="{ blocked: place.blocked }" @click="toggleBlock('place')">{{ place.value }}</span>, con
          <span :class="{ blocked: trigger.blocked }" @click="toggleBlock('trigger')">{{ trigger.value }}</span>.
        </p>
        <p>
          (Ten en cuenta que <span :class="{ blocked: peculiarity.blocked }" @click="toggleBlock('peculiarity')">{{ peculiarity.value }}</span>).
        </p>
        <p>
          Y hay un detalle: <span :class="{ blocked: constraint.blocked }" @click="toggleBlock('constraint')">{{ constraint.value }}</span>.
        </p>
      </section>
    </main>

    <section class="credits center">
      <p>
        Imágenes: <a href="http://www.freepik.com">Designed by Freepik</a>.
        <br>
        Basado en <a href="https://blog.reedsy.com/plot-generator/">Plot Generator</a>.
      </p>
    </section>
    <footer>
      <div class="center">
        <p>
          Una ocurrencia de <a href="https://github.com/luvejo">Luis Velásquez</a>.
        </p>
      </div>
    </footer>
  </div><!-- .container -->
</template>

<script>
const genres = [
  { name: 'Romance', id: 'romance' }
]

function random (list) {
  const index = Math.floor(
    Math.random() * list.length)
  return list[index]
}

export default {
  head() {
    return {
      title: 'Generador de Argumentos'
    }
  },
  async asyncData () {
    const genre = random(genres)
    const { default: lists } = await import(`@/data/${genre.id}_lists.js`)

    return {
      protagonist: { value: '', blocked: false },
      protagonistAbout: { value: '', blocked: false },
      secondaryCharacter: { value: '', blocked: false },
      secondaryCharacterAbout: { value: '', blocked: false },
      genre: { value: '', blocked: false },
      subject: { value: '', blocked: false },
      place: { value: '', blocked: false },
      trigger: { value: '', blocked: false },
      peculiarity: { value: '', blocked: false },
      constraint: { value: '', blocked: false },
      lists,
      genres
    }
  },
  created () {
    this.generate()
  },
  methods: {
    set_random (prop, list, gender) {
      if (this[prop].blocked) {

        let sentence_gender = 0
        list.forEach(pair_of_sentences => {
          if (typeof pair_of_sentences === 'object') {
            pair_of_sentences.forEach((sentence, i) => {
              if (this[prop].value === sentence) {
                sentence_gender = i
              }
            })
          }
        })

        return sentence_gender
      }

      const gendered_sentences = random(list)
      let choice = null

      if (typeof gendered_sentences === 'object') {

        if (typeof gender === 'undefined') {
          choice = random(gendered_sentences)

        } else if (gendered_sentences.length > 1) {
          choice = gendered_sentences[gender]

        } else {
          choice = gendered_sentences[0]
        }

        this[prop].value = choice
        return gendered_sentences.findIndex(e => choice === e)

      } else {
        this[prop].value = gendered_sentences
      }
    },
    generate () {
      const protagonistGender = this.set_random('protagonist', this.lists.characters)
      this.set_random('protagonistAbout', this.lists.abouts, protagonistGender)

      const secondaryCharacterGender = this.set_random('secondaryCharacter', this.lists.characters)
      this.set_random('secondaryCharacterAbout', this.lists.abouts, secondaryCharacterGender)

      this.set_random('genre', this.lists.genres)
      this.set_random('subject', this.lists.subjects)
      this.set_random('place', this.lists.places)
      this.set_random('trigger', this.lists.triggers)
      this.set_random('peculiarity', this.lists.peculiarities)
      this.set_random('constraint', this.lists.constraints)
    },
    toggleBlock (prop) {
      this[prop].blocked = !this[prop].blocked
    }
  }
}
</script>

<style lang="scss">
@import "@/assets/css/main";

body {
  position: absolute;
  top: 0;
  left: 0;
  margin: 0px;
  padding: 0px;
  width: 100%;
}

h1 {
  font-size: 2em;
  margin: 0px;
  color: $white;
  font-family: "Patrick Hand", "Open Sans", Arial, "Sans-Serif";
  line-height: 1em;
}

button {
   cursor: pointer;
}

.center {
  max-width: 600px;
  margin: 0 auto;
}

header {
  background-color: $middlepink;

  .center {
    padding-bottom: 20px - 5px;
    padding: 20px;
    background: url("~assets/img/bulb.png") top right no-repeat $middlepink;
    background-position: right 10px top 0px;
  }

  p {
    color: $white;
    font-size: 1em;
    margin: 20px 0;
  }

  select {
    border: none;
    background-color: transparent;
    color: $white;
    padding: 10px 10px;
    padding-right: 20px;
    border-radius: $radius;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    background: url("~assets/img/drop-down.png") right no-repeat $lightpink;
    margin-bottom: 5px;
    line-height: 1em;
  }

  select::-ms-expand {
    display: none;
  }

  button {
    border: none;
    background-color: white;
    padding: 10px 10px;
    border-radius: $radius;
    margin-left: 10px;
    line-height: 1em;

    p {
      align-items: bottom;
      margin: 0px;
      display: inline-block;
      color: $darkpink;
    }
  }

  svg {
    height: 15px;
    vertical-align: middle;
    margin-left: 10px;

    path {
      color: $darkpink;
    }
  }

  .controls {

    &.fixed {
      position: fixed;
      background-color: $middlepink;
      top: 0;
      left: 0;
      right: 0;
      width: 100%;
      padding: 18px 0;

      form {
        display: block;
        padding: 0 18px;
      }
    }
  }

  form {
    text-align: right;
  }
}

main {
  padding: 20px 20px;
  padding-bottom: 40px;
  max-width: 600px;
  margin: 0 auto;
  background: url("~assets/img/background.png") top center repeat;
}

main h2 {
  font-size: .8em;
  margin: 0 0 .2em 0;
}

main section:not(:first-child) {
  margin-top: 20px;
}

main section p:not(:first-of-type) {
  margin-top: 20px;
}

main p {
  font-size: 1em;
  margin-top: 0px;
  line-height: 1.3em;
}

main span {
  font-size: 1em;
  color: $lightpink;

  &:hover {
    cursor: pointer;
  }

  &.blocked {
    color: $black;
    padding: 0px;
    border-bottom: 2px solid $gray;
    border-radius: $radius;
  }
}

section.credits {
  padding: 20px;
  background-color: $gray;
  text-align: center;
}

footer {
  width: 100%;
  text-align: center;
  font-size: 1em;

  .center {
    padding: 10px 2.5%;
    background-color: $black;
  }

  p {
    margin: 0px;
    color: $white;
  }

  a {
    font-size: 1em;
    font-weight: bold;
    color: $white;
    text-decoration: none;
  }

  a:last-child {
    color: $lightpink;
  }
}

@media only screen and (min-width: 600px) {
  * {
    font-size: 22px;
  }

  h1 {
    font-size: 2.2em;
  }

  body {
    background: url("~assets/img/background.png") top center repeat;
  }

  header {
    background-color: transparent;
    .center {
      padding: 20px 40px;
      background-position: right 20px top 0px;
    }
  }

  main {
    background: $white;
    border-left: 1px solid $gray;
    border-right: 1px solid $gray;
    padding: 40px 40px;
    padding-bottom: 80px;
  }
}

</style>
