<template>
    <div>
        <header class="bg-blue-500 text-white py-4 px-6 flex justify-between items-center text-lg">
            <router-link :to="{ name: 'dnevniunos' }">
            <custom-button :dark="true" :color="'primary'" class="m-2 ">
              Dnevni unos
            </custom-button>
            </router-link>
        </header>
    </div>
</template>

<script >
import CustomButton from '@/components/CustomButton.vue';

export default {
  components: {
    CustomButton,
  },
  props: ['value'],
};
</script>