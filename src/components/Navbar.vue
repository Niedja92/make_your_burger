<template>
  <div>
    <nav id="nav">
      <!-- utiliza-se o "to" para indicar qual rota o link irá representar -->
      <router-link to="/" id="logo-url">
        <!-- passa as propriedades para que apareça dinamicamente usando v-bind ":" -->
        <img :src="logo" :alt="alt" id="logo" />
      </router-link>
      <router-link to="/">Home</router-link> |
      <router-link to="/pedidos">Pedidos</router-link>
    </nav>
  </div>
</template>
<script>
//o name deve ter o mesmo nome do componente
export default {
  name: "Navbar",
  props: ["logo", "alt"],
};
</script>
<style scoped>
#nav {
  background-color: #222;
  border-bottom: 4px solid #111;
  padding: 15px 50px;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

#nav #logo-url {
  margin: auto;
  margin-left: 0;
}

#logo {
  width: 40px;
  height: 40px;
}

#nav a {
  color: #fcba03;
  text-decoration: none;
  margin: 12px;
  transition: 0.5s;
}

#nav a:hover {
  color: #fff;
}
</style>
