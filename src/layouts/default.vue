<!-- eslint-disable vue/html-closing-bracket-spacing -->
<!-- eslint-disable vue/max-attributes-per-line -->
<template>
  <v-layout>
    <v-app-bar title="Catálogo de Videojuegos"/>

    <v-navigation-drawer>
      <v-list>
        <v-list-item title="Menú de navegación"/>
        <v-list-item title="Inicio" to="/"/>
        <v-list-item title="Juegos" to="/juegos"/>
        <v-list-item title="Consolas" to="/consolas"/>
        <v-list-item title="Plataformas" to="/plataformas"/>
        <v-list-item title="Wishlist" to="/wishlist"/>
        <v-list-item title="Sesiones" to="/sesiones"/>
        <v-list-item title="Estadisticas" to="/estadisticas"/>
        <v-list-item title="Compras" to="/compras"/>
      </v-list>
    </v-navigation-drawer>
    <v-main class="d-flex align-center justify-center" style="min-height: 945px;">
      <router-view />
    </v-main>

    <AppFooter />
  </v-layout>
</template>

<script setup>
  //
</script>
