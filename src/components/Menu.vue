<template>
  <div class="ui secondary menu">
    <div class="ui container">
      <div class="left menu">
        <router-link class="item" to="/">
          <img
            class="ui small image"
            src="../assets/logo.png"
            alt="Ecommerce"
          />
          <template v-for="category in categories" :key="category.id">
            <router-link class="item" :to="category.slug">
              {{ category.title }}
            </router-link>
          </template>
        </router-link>
      </div>

      <div class="right menu">
        <router-link class="item" to="/login" v-if="!token">
          Iniciar sesión
        </router-link>

        <template v-if="token">
          <span class="item">Usuario : {{ username }} </span>
          <router-link class="item" to="/orders">Pedidos</router-link>
          <span class="ui item cart" @click="openCart">
            <i class="shopping cart icon"></i>
          </span>
          <span class="ui item logout" @click="logout">
            <i class="sign-out icon"></i>
          </span>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import { useStore } from "vuex";
import { getTokenApi, deleteTokenApi } from "../api/token";
import { GetUserApi } from "../api/user";
import { getCategoriesApi } from "../api/category";
import jwtDecode from "jwt-decode";


export default {
  name: "Menu",
  setup() {

    let categories = ref(null);
    let user = ref(null);
    let username = ref("");
    let id_user = ref(0);
    const token = getTokenApi();
    const store = useStore();

   
    onMounted(async () => {

      if (token){
        const { id } = jwtDecode(token);
        const responseUser = await GetUserApi(id);
        user.value = responseUser;
        username.value = user.value.username;
        //console.log(username.value);
        id_user.value = id;
        
      }
      
      const response = await getCategoriesApi();
      categories.value = response;
      });

    //console.log(id_user.value);

    const logout = () => {
      deleteTokenApi();
      location.replace("/");
    };

    const openCart = () => {
      store.commit("setShowCart", true);
    };

    return {
      token,
      logout,
      categories,
      openCart,
      user,
      username,
      id_user
    };
  },
};
</script>

<style lang="scss" scoped>
.ui.menu.secondary {
  background-color: #16202b;

  .item {
    color: #fff;
    &:hover {
      color: #1fa1f1;
    }
  }

  .menu.left {
    width: 50%;
    .ui.image {
      width: 40px;
    }
  }

  .menu.right {
    width: 50%;
    justify-content: flex-end;

    .logout,
    .cart {
      &:hover {
        cursor: pointer;
      }
    }
  }
}
</style>