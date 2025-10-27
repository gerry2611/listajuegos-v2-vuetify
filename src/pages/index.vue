<template>
  <v-img
    src="../assets/Logo_Principal_Catalogo.png"
    alt="Logo Principal Catalogo"
    height="256"
    width="128"
    color="white"
    position="right"
  />

  <div
    class="d-lg-none ga-3 align-content-center"
    style="position: absolute; top: 40%; right: 20%; left: 20%;"
  >
    <v-btn
      text="Juegos"
      to="/juegos"
    />
    <v-btn
      text="Cuentas"
      to="/cuentas"
    />
    <v-btn
      text="Wishlist"
      to="/wishlist"
    />
    <v-btn
      text="Compras"
      to="/compras"
    />
  </div>
</template>

<script setup>
  //
</script>
