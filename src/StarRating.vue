<template>  <div class="star-rating">
  <span v-for="(star, index) in fullStars" :key="'full-' + index" class="star full">★</span>
    <span v-if="hasHalfStar" class="star half">
      <span class="half-left">★</span>
      <span class="half-right">☆</span>
    </span>

  <span v-for="(star, index) in emptyStars" :key="'empty-' + index" class="star empty">☆</span>  </div>
</template>
<script>
export default {
  props: {
    rating: {
      type: Number,
      required: true
    }
  },
  computed: {
    fullStars() {
      return Math.floor(this.rating);
    },
    hasHalfStar() {
      return this.rating % 1 >= 0.5;
    },
    emptyStars() {
      return 5 - this.fullStars - (this.hasHalfStar ? 1 : 0);
    }
  }
};</script>
<style>
.star {
  font-size: 24px;
  color: #FFD700;
  position: relative;
  display: inline-block;
}

.star.empty {
  color: #ccc;
}

.star.half {
  position: relative;
  display: inline-block;
  width: 12px;
}

.star.half .half-left {
  position: absolute;
  overflow: hidden;
  width: 12px;
  color: #FFD700;
}

.star.half .half-right {
  color: #ccc;
}
</style>