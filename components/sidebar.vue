<template>
  <section>
    <b-sidebar
      type="is-light"
      :fullheight="fullheight"
      :fullwidth="fullwidth"
      :overlay="false"
      :right="right"
      v-model="open"
    >
      <div class="p-1">
        <img 
          src="favicon.ico"
          alt="TulMash"
        />
        <b-menu>
          <b-menu-list label="Меню">
            <b-menu-item  tag="router-link" :to="{name: 'home'}" icon="home" label="Главная страница"></b-menu-item>
            <b-menu-item v-if="user_role == 'admin'" icon="settings">
              <template  #label="props">
                Администратор
                <b-icon class="is-pulled-right" :icon="props.expanded ? 'menu-down' : 'menu-up'"></b-icon>
              </template>
              <b-menu-item tag="router-link" :to="{name: 'add_new_worker'}" icon="account" label="Добавить сотрудника"></b-menu-item>
            </b-menu-item>
            <b-menu-item icon="settings">
              <template icon="dollar-sign" #label="props">
                Продажи
                <b-icon class="is-pulled-right" :icon="props.expanded ? 'menu-down' : 'menu-up'"></b-icon>
              </template>
              <b-menu-item tag="router-link" :to="{name: 'clients_options'}" icon="account" label="Клиенты"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'add_new_worker'}" icon="account" label="Заказы"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'marketing_docs'}" icon="file-document" label="Заказы и документы"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'common_clients'}" icon="account" label="Все клиенты"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'add_new_worker'}" icon="account" label="Поставщики"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'add_new_worker'}" icon="account" label="Готовая продукция"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'add_new_worker'}" icon="account" label="Покупные"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'add_new_worker'}" icon="account" label="Статистика"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'add_new_worker'}" icon="account" label="Сводка"></b-menu-item>
              <b-menu-item tag="router-link" :to="{name: 'marketing_demands'}" icon="clipboard-list" label="Заявки"></b-menu-item>
            </b-menu-item>
            <b-menu-item icon="account" label="My Account">
              <b-menu-item label="Account data"></b-menu-item>
              <b-menu-item label="Addresses"></b-menu-item>
            </b-menu-item>
          </b-menu-list>
          <b-menu-list>
            <b-menu-item  tag="router-link" :to="{name: 'about'}" icon="information-outline" label="Справка"></b-menu-item>
          </b-menu-list>
          <b-menu-list label="Действия">
            <b-menu-item @click="logout" icon="door-open" label="Выход"></b-menu-item>
          </b-menu-list>
        </b-menu>
      </div>
    </b-sidebar>
    <b-button type="is-light" class="show_bar" title="Открыть меню" @click="open = true">Меню <i class="fas fa-bars "></i></b-button>
  </section>
</template>

<script>
module.exports = {
  data() {
    return {
      open: false,
      overlay: true,
      fullheight: true,
      fullwidth: false,
      right: false,
      user_role: store.getters.USER_ROLE
    }
  },
  methods: {
    
    logout: function() {
        app.$store.dispatch('LOGOUT_AUTH')
        app.$store.dispatch('LOGOUT_ID')
        app.$store.dispatch('LOGOUT_ROLE')
        localStorage.clear()
        router.push('/login')
        },
    
  }
};
</script>

<style>
.p-1 {
  padding: 1em;
}
 
.show_bar {
    position: sticky;
    top:0;
    padding: 10px;
}
</style>