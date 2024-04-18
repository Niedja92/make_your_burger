<!-- importar este componente no views Home -->
<template>
  <div>
    <Message :msg="msg" v-show="msg" />
    <div>
      <form id="burger-form" method="POST" @submit="createBurger">
        <div class="input-container">
          <label for="name">Nome do cliente:</label>
          <input
            type="text"
            id="nome"
            name="nome"
            v-model="nome"
            placeholder="Digite o seu nome"
          />
        </div>
        <div class="input-container">
          <label for="pao">Escolha o pão:</label>
          <select name="pao" id="pao" v-model="pao">
            <option value="">Selecione o seu pão</option>
            <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">
              {{ pao.tipo }}
            </option>
          </select>
        </div>
        <div class="input-container">
          <label for="carne">Escolha a carne do seu Burger:</label>
          <select name="carne" id="carne" v-model="carne">
            <option value="">Selecione o tipo de carne</option>
            <!-- antes da alteração para puxar os dados dinamicamente <option value="maminha">Maminha</option> -->
            <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">
              {{ carne.tipo }}
            </option>
          </select>
        </div>
        <div id="opcionais-container" class="input-container">
          <label id="opcionais-title" for="opcionais"
            >Selecione os opcionais:</label
          >
          <div
            class="checkbox-container"
            v-for="opcional in opcionaisData"
            :key="opcional.id"
          >
            <input
              type="checkbox"
              name="opcionais"
              v-model="opcionais"
              :value="opcional.tipo"
            />
            <span>{{ opcional.tipo }}</span>
          </div>
        </div>
        <div class="input-container">
          <input type="submit" class="submit-btn" value="Criar meu Burger!" />
        </div>
      </form>
    </div>
  </div>
</template>

<!-- após criar os dados estáticos e a estilização, agora é a vez do método data() para a inlusão das propriedades -->
<script>
import Message from "./Message.vue";

export default {
  name: "BurgerForm",
  data() {
    return {
      //dados que vêm do servidor em plural
      paes: null,
      carnes: null,
      opcionaisData: null,
      //dador que são enviados ao servidor no singular
      nome: null,
      pao: null,
      carne: null,
      opcionais: [],
      msg: null,
    };
  },
  // 'GET' cria-se "methods", pois o sistema trabalhará com o backend trazendo os dados dos ingredientes ('GET')
  methods: {
    async getIngredientes() {
      const request = await fetch("http://localhost:3000/ingredientes");
      const data = await request.json();
      //console.log(data);
      //dados dos ingredientes preenchidos pelos dados vindo do servidor
      this.paes = data.paes;
      this.carnes = data.carnes;
      this.opcionaisData = data.opcionais;
    },
    // 'POST' - após o createBurger, incluímos o evento @submit na tag <form> para ativar este método
    async createBurger(e) {
      e.preventDefault();
      //criamos um objeto com os dados
      const data = {
        nome: this.nome,
        carne: this.carne,
        pao: this.pao,
        opcionais: Array.from(this.opcionais), // passando Array para puxar os dados corretamente
        status: "Solicitado",
      };
      //console.log(data);
      const dataJson = JSON.stringify(data);

      // após a requisição devem ser passados outros argumentos para que o programa não entenda que se trata de uma 'GET'
      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        header: { "Content-Type": "application/json" },
        body: dataJson,
      });
      // aguarda a resposta
      const res = await req.json();
      // console.log(res);

      // colocar mensagem no sistema
      this.msg = `Pedido Nº ${res.id} realizado com sucesso!`;

      // limpar mensagem (após o tempo determinado)
      setTimeout(() => (this.msg = ""), 3000);

      // limpar os campos
      this.nome = "";
      this.carne = "";
      this.pao = "";
      this.opcionais = [];
    },
  },
  // criando a chamada do método (quando o componente for montado, acontecerá...)
  mounted() {
    this.getIngredientes();
  },
  components: {
    Message,
  },
};
</script>

<style scoped>
#burger-form {
  max-width: 400px;
  margin: 0 auto;
}

.input-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

label {
  font-weight: bold;
  margin-bottom: 15px;
  color: #222;
  padding: 5px 10px;
  border-left: 4px solid #fcba03;
}

input,
select {
  padding: 5px 10px;
  width: 300px;
}

#opcionais-container {
  flex-direction: row;
  flex-wrap: wrap;
}

#opcionais-title {
  width: 100%;
}

.checkbox-container {
  display: flex;
  align-items: flex-start;
  width: 50%;
  margin-bottom: 20px;
}

.checkbox-container span,
.checkbox-container input {
  width: auto;
}

.checkbox-container span {
  margin-left: 6px;
  font-weight: bold;
}
.submit-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.submit-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
