<template>
  <div id="burger-table" v-if="burgers">
    <Message :msg="msg" v-show="msg" />
    <div>
      <div id="burger-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente:</div>
        <div>Pão:</div>
        <div>Carne:</div>
        <div>Opcionais:</div>
        <div>Ações:</div>
      </div>
    </div>
    <div id="burger-table-rows">
      <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.nome }}</div>
        <div>{{ burger.pao }}</div>
        <div>{{ burger.carne }}</div>
        <div>
          <ul v-for="(opcional, index) in burger.opcionais" :key="index">
            <li>{{ opcional }}</li>
          </ul>
        </div>
        <div>
          <select
            name="status"
            class="status"
            @change="updateBurger($event, burger.id)"
          >
            <!-- passa o evento @change para que quando houver uma mudança essa atualização seja feita  -->
            <option
              :value="s.tipo"
              v-for="s in status"
              :key="s.id"
              :selected="burger.status == s.tipo"
            >
              {{ s.tipo }}
            </option>
          </select>
          <!-- cria o evento de click para que o botão delete possa acessar todos os burgers que estão em loop (v-for) -->
          <button class="delete-btn" @click="deleteBurger(burger.id)">
            Cancelar
          </button>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <h2>Não há pedidos no momento!</h2>
  </div>
</template>

<script>
import Message from "./Message.vue";

export default {
  name: "Dashboard",
  data() {
    return {
      burgers: null,
      burger_id: null,
      status: [],
      msg: null,
    };
  },
  components: {
    Message,
  },
  methods: {
    // parte de gerência dos pedidos, onde é feita e troca do valor de "null" do data() para o que veio do servidor
    async getPedidos() {
      const req = await fetch("http://localhost:3000/burgers");
      const data = await req.json();
      this.burgers = data;
      // resgata os status dos pedidos
      this.getStatus();
    },
    async getStatus() {
      const req = await fetch("http://localhost:3000/status");
      const data = await req.json();
      this.status = data;
    },
    async deleteBurger(id) {
      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: "DELETE",
      });

      const res = await req.json();

      // colocar mensagem no sistema
      this.msg = `Pedido removido com sucesso!`;

      // limpar mensagem (após o tempo determinado)
      setTimeout(() => (this.msg = ""), 3000);

      this.getPedidos();
    },
    // atualizando status do pedido
    async updateBurger(event, id) {
      // faz com que saibamos qual o status que o usuário admin está colocando no "banco"
      const option = event.target.value;
      // faz com o que os dados possam ir para o json server em forma de string.
      // status: option = atualiza o status do pedido
      const dataJson = JSON.stringify({ status: option });

      const req = await fetch(`http://localhost:3000/burgers${id}`, {
        // usa-se o método PATCH para alterar apenas o que foi enviado, as demais informações permanecem as mesmas
        method: "PATCH",
        headers: { "Content-Type": "application/json" },
        body: dataJson,
      });
      const res = await req.json();

      // colocar mensagem no sistema
      this.msg = `Pedido Nº ${res.id} foi atualizado para ${res.status}!`;

      // limpar mensagem (após o tempo determinado)
      setTimeout(() => (this.msg = ""), 3000);

      console.log(res);
    },
  },
  // quando o componente for montado
  mounted() {
    this.getPedidos();
  },
};
</script>

<style scoped>
#burger-table {
  max-width: 1200px;
  margin: 0 auto;
}

#burger-table-heading,
#burger-table-rows,
.burger-table-row {
  display: flex;
  flex-wrap: wrap;
}

#burger-table-heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}

.burger-table-row {
  width: 100%;
  padding: 12px;
  border-bottom: 1px solid #ccc;
}

#burger-table-heading div,
.burger-table-row div {
  width: 19%;
}

#burger-table-heading .order-id,
.burger-table-row .order-number {
  width: 5%;
}

select {
  padding: 12px 6px;
  margin-right: 12px;
}

.delete-btn {
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

.delete-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
