<template>
  <div>
    <!-- Navbar Component -->
    <NavbarComponent />

    <!-- Sections -->
    <section id="home">🏠 Welcome to Home</section>

    <section id="services">
      <ServicesComponent />
    </section>

    <!-- Footer Component -->
    <footerComponent />
  </div>
</template>

<script>
import NavbarComponent from '../../components/navbars/homeNavbar.vue';
import ServicesComponent from './servicesComponent.vue';
import footerComponent from './footerSection.vue';

export default {
  name: 'homeComponent',
  components: {
    NavbarComponent,
    ServicesComponent,
    footerComponent,
  },
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Arial, sans-serif;
  scroll-behavior: smooth;
}

body {
  background: url('https://source.unsplash.com/1600x900/?technology,robot') no-repeat center center/cover;
  height: 100vh;
  color: white;
  margin: 0;
  padding: 0;
}

section {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 40px;
}
</style>