<template>
  <div>
    <!-- Grid listesi -->
    <div class="grid-list">
      <!-- Her bir öğe için grid item -->
      <div v-for="item in visibleItems" :key="item.id" class="grid-item">
        <!-- Öğe resmi -->
        <img
          :src="item.image"
          alt="Item Image"
          @click="handleItemClick(item.id)"
          :class="{ 'selected': selectedItemId === item.id }"
        />
        <!-- Öğe metni -->
        <p>{{ item.text }}</p>
      </div>
    </div>

    <!-- Sayfa altına scroll yapıldıkça yeni verileri yüklemek için alt bölüm işareti -->
    <div ref="bottom" class="bottom-marker"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Tüm veriler
      allItems: [],
      // Sayfada görünen veriler
      visibleItems: [],
      // Sayfa numarası
      page: 1,
      // Sayfadaki eleman sayısı
      itemsPerPage: 20,
      // Seçilen öğe ID'si
      selectedItemId: null,
    };
  },
  mounted() {
    // Intersection Observer'ı kur
    this.setupIntersectionObserver();
    // Sayfa yüklendiğinde başlangıç verilerini yükle
    this.loadMore();
  },
  methods: {
    // Intersection Observer'ı kur
    setupIntersectionObserver() {
      const options = {
        root: null,
        rootMargin: '0px',
        threshold: 0.1,
      };

      // Intersection Observer'ı başlat
      this.intersectionObserver = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          // Eğer sayfanın altına gelindiğinde
          if (entry.isIntersecting) {
            // Yeni verileri yükle
            this.loadMore();
          }
        });
      }, options);

      // Observer'ı sayfa altındaki bölgeye ekle
      this.intersectionObserver.observe(this.$refs.bottom);
    },
    // Öğe tıklandığında
    handleItemClick(itemId) {
      // Seçilen öğe ID'sini güncelle
      this.selectedItemId = itemId;
    },
    // Yeni verileri yüklemek için metot
    loadMore() {
      // Örnek: Lorem Picsum hizmetini kullanarak rastgele resimler ekleniyor
      for (let i = 0; i < this.itemsPerPage; i++) {
        this.allItems.push({
          id: this.allItems.length + 1,
          image: `https://picsum.photos/200/300?random=${this.allItems.length + 1}`,
          text: `Item ${this.allItems.length + 1}`,
        });
      }

      // Sayfa numarasını güncelle
      this.page += 1;

      // Gözüken verileri güncelle
      const startIndex = (this.page - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      this.visibleItems = this.allItems.slice(0, endIndex);
    },
  },
};
</script>

<style>
/* Stil sınıfları */
.grid-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 16px;
}

.grid-item {
  border: 1px solid #ccc;
  border-radius: 8px;
  overflow: hidden;
  text-align: center;
}

.selected {
  background-color: red; /* Örneğin seçilen öğenin arka planını kırmızıya ayarlar */
}

.bottom-marker {
  height: 1px; /* Sayfa altına scroll yapıldıkça bu bölgeyi gözlemleyecek Intersection Observer'ı kurmak için kullanılır */
}

button {
  margin-top: 16px;
}
</style>
