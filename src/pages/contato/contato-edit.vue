<template>
  <q-page padding>
    <div class="q-pa-md" style="max-width: 1200px">
      <div class="row q-mb-md">
        <div class="col text-h5">cliente</div>
        <div class="col-auto"># {{ campos._id }}</div>
      </div>
      <q-form @submit="$tryLoading(salvar)" class="q-gutter-sm">
        <div class="row items-baseline">
          <q-input v-model="campos.nome" label="nome" class="col-12 q-pr-md" outlined autofocus
            :rules="[ $rules.obrigatorio ]" />
        </div>
        <div class="row items-baseline">
          <q-input v-model="campos.cpf" label="cpf" class="col-12 col-md-6 q-pr-md" outlined />
          <q-input v-model="campos.email" label="email" class="col-12 col-md-6 q-pr-md" outlined />
        </div>
        <div class="row items-baseline">
          cadastro: <strong class="q-ml-md">{{ $format.datetime(campos.datahora_cadastro) }}</strong>
        </div>
        <div>
          <q-btn label="salvar" type="submit" color="primary" no-caps />
          <q-btn v-if="campos._id" label="remover" color="negative" class="q-ml-sm" no-caps
            @click="$tryLoading(remover)" />
          <q-btn label="voltar" color="primary" flat class="q-ml-sm" no-caps :to="{ name: 'contato-list' }" />
        </div>
      </q-form>
    </div>
  </q-page>
</template>

<script>
  export default {
    name: 'contato-edit',

    data() {
      return {
        campos: {}
      }
    },

    methods: {
      async atualizar() {
        if (!this.$route.params.id) {
          this.$set(this.campos, 'datahora_cadastro', new Date())
        }

        try {
          $loading.show()
          if (this.$route.params.id) {
            const resposta = await $backend('get', 'contato/' + this.$route.params.id)
            this.campos = resposta.data
          }
        } catch (erro) {
          $notifyError('erro na consulta', erro)
        } finally {
          $loading.hide()
        }
      },

      async salvar() {
        if (this.campos._id) {
          await $backend('put', 'contato/' + this.campos._id, { ...this.campos, _id: undefined })
        } else {
          await $backend('post', 'contato', this.campos)
        }
        $notifyPositive('contato salvo com sucesso')
        this.$router.go(-1)
      },

      async remover() {
        await $backend('delete', 'contato/' + this.campos._id)
        $notifyPositive('contato removido com sucesso')
        this.$router.go(-1)
      }
    },

    mounted() {
      this.atualizar()
    }
  }
</script>