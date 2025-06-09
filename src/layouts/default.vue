<!-- eslint-disable vue/html-closing-bracket-spacing -->
<!-- eslint-disable vue/max-attributes-per-line -->
<template>
  <v-layout>
    <v-app-bar title="Catálogo de Videojuegos"/>

    <v-navigation-drawer expand-on-hover rail>
      <v-list density="compact" nav>
        <v-list-item prepend-icon="mdi-home" title="Inicio" to="/"/>
        <v-list-item prepend-icon="mdi-gamepad" title="Juegos" to="/juegos"/>
        <v-list-item prepend-icon="mdi-controller" title="Consolas" to="/consolas"/>
        <v-list-item prepend-icon="mdi-account" title="Plataformas" to="/plataformas"/>
        <v-list-item prepend-icon="mdi-cash" title="Cuentas" to="/cuentas"/>
        <v-list-item prepend-icon="mdi-format-list-checks" title="Wishlist" to="/wishlist"/>
        <v-list-item prepend-icon="mdi-play-circle" title="Sesiones" to="/sesiones"/>
        <v-list-item prepend-icon="mdi-gauge" title="Estadisticas" to="/estadisticas"/>
        <v-list-item prepend-icon="mdi-cart-variant" title="Compras" to="/compras"/>
      </v-list>
    </v-navigation-drawer>
    <v-main class="d-flex" style="min-height: 650px;">
      <router-view />
    </v-main>

    <AppFooter />
  </v-layout>
</template>

<script setup>
  //
</script>
