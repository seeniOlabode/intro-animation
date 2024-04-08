<template>
  <div class="site-transition fouc-cover">
    <div
      class="site-transition__image-row"
      v-for="(image, index) in images"
      :key="image + index"
    >
      <div class="image-row__image-container" v-for="i of 7" :key="i + image">
        <img class="image-container__image" :src="image" alt="" />
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent } from "@vue/composition-api";
import gsap from "gsap";

export default defineComponent({
  setup() {
    const images = ref([
      "https://i.pinimg.com/564x/31/af/b4/31afb490c98a73f76a5c91e84390c098.jpg",
      "https://i.pinimg.com/originals/d1/c4/0b/d1c40b0523b012678661828c454a421a.jpg",
      "https://i.pinimg.com/originals/0b/7b/11/0b7b1130a66cecc8a94665785cd5137c.jpg",
      "https://i.pinimg.com/originals/75/55/b3/7555b31f8bc38b906177c0e9275c54df.jpg",
      "https://i.pinimg.com/564x/0b/92/64/0b92647866420e672bbcc4bcee9c0a42.jpg",
    ]);

    const tl = gsap.timeline();

    onMounted(() => {
      const transitionContainer = select(".site-transition.fouc-cover");
      const allImageRows = selectAll(".site-transition__image-row");
      const firstSetRows = selectAll(
        ".site-transition__image-row:nth-child(odd)"
      );
      const secondSetRows = selectAll(
        ".site-transition__image-row:nth-child(even)"
      );

      const offset = window.innerHeight - allImageRows[0].clientHeight;
      tl.call(() => {
        transitionContainer.style.setProperty("--offset", offset + "px");
      })
        .call(() => {
          transitionContainer.classList.toggle("fouc-cover");
        })
        .addLabel("start")
        .from(
          firstSetRows,
          {
            y: -firstSetRows[0].clientHeight,
            ease: "expo.inOut",
            duration: 3,
          },
          "start"
        )
        .from(
          secondSetRows,
          {
            y: window.innerHeight,
            ease: "expo.inOut",
            duration: 3,
          },
          "start"
        );
      firstSetRows.forEach((row) => {
        const imagesInRow = selectAllFrom(".image-row__image-container", row);

        imagesInRow.forEach((image, i) => {
          tl.from(
            image,
            {
              y: -3000 * (imagesInRow.length - 1 - i),
              duration: 4,
              ease: "circ.out",
            },
            "start"
          );
        });
      });
      secondSetRows.forEach((row) => {
        const imagesInRow = selectAllFrom(".image-row__image-container", row);

        imagesInRow.forEach((image, i) => {
          tl.from(
            image,
            {
              y: 3000 * i,
              duration: 4,
              ease: "circ.out,",
            },
            "start"
          );
        });
      });

      const mode = window.innerWidth > window.innerHeight ? "Width" : "Height";

      console.log(mode);

      tl.to(
        transitionContainer,
        {
          scale:
            window[`inner${mode}`] /
            select(".image-row__image-container")[`client${mode}`],
          ease: "expo.inOut",
          duration: 2,
        },
        ">-=0.5"
      );
    });

    return {
      images,
    };
  },
  computed: {
    images() {},
  },
});
</script>

<style>
body {
  overflow: hidden;
}

.site-transition {
  width: 100vw;
  height: 100vh;
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 10px;
  overflow: hidden;
  --offset: 0;
}

.site-transition__image-row {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.site-transition__image-row:nth-child(odd) {
  transform: translateY(calc(var(--offset) / 2));
}

.image-row__image-container {
  aspect-ratio: 3/2;
  overflow: hidden;
}

.image-container__image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.fouc-cover {
  visibility: hidden;
}
</style>