<script>
export default {
  name: "AnnihilationMatterTab",
  data() {
    return {
      matter: new Decimal(0),
      dimensionsAnnihilated: 0,
      infinityAnnihilationUnlocked: false,
      dimensions: [],
      canAnnihilateInfinityColumn: false,
      infinityColumnAnnihilated: false,
      nextMatterGain: new Decimal(0),
      canReset: false,
      resetRequirement: new Decimal(0),
      resetRequirementText: "",
      infinityColumnCost: new Decimal(0),
    };
  },
  methods: {
    update() {
      this.matter.copyFrom(Annihilation.matter);
      this.dimensionsAnnihilated = Annihilation.dimensionCount;
      this.infinityAnnihilationUnlocked = Annihilation.dimensionCount >= 5;
      this.dimensions = Array.range(1, 8).map(tier => ({
        tier,
        cost: Annihilation.dimensionCost(tier),
        isAnnihilated: Annihilation.isDimensionAnnihilated(tier),
        canAnnihilate: Annihilation.canAnnihilateDimension(tier),
      }));
      this.canAnnihilateInfinityColumn = Annihilation.canAnnihilateInfinityColumn(0);
      this.infinityColumnAnnihilated = Annihilation.isInfinityColumnAnnihilated(0);
      this.nextMatterGain.copyFrom(Annihilation.matterGain);
      this.canReset = Annihilation.canReset;
      this.resetRequirement.copyFrom(Annihilation.resetRequirement);
      this.resetRequirementText = Annihilation.hasAnnihilated
        ? formatAnnihilation(Annihilation.resetRequirement, 2)
        : "9ee15";
      this.infinityColumnCost.copyFrom(Annihilation.infinityColumnCost(0));
    },
    annihilateDimension(tier) {
      Annihilation.annihilateDimension(tier);
    },
    annihilateInfinityColumn() {
      Annihilation.annihilateInfinityColumn(0);
    },
    annihilate() {
      Annihilation.reset();
    },
    replayQuotes() {
      Quotes.annihilation.first.present();
    },
  },
};
</script>

<template>
  <div class="l-annihilation-tab">
    <h2>Annihilation Matter</h2>
    <button
      class="c-annihilation-quotes"
      @click="replayQuotes"
    >
      Annihilation Quotes
    </button>
    <button
      class="c-annihilation-reset"
      :disabled="!canReset"
      @click="annihilate"
    >
      <template v-if="canReset">
        Annihilate and begin again<br>
        Gain {{ formatAnnihilation(nextMatterGain, 2) }} Annihilation Matter and 1 Annihilation Perk
      </template>
      <template v-else>
        Reach {{ resetRequirementText }} Antimatter to Annihilate
      </template>
    </button>
    <div class="c-annihilation-tab__amount">
      {{ formatAnnihilation(matter, 2) }}
    </div>
    <p>Spend Matter to annihilate Dimensions and permanently strengthen Antimatter production.</p>
    <p>
      {{ formatAnnihilation(dimensionsAnnihilated, 0) }}/8 Dimensions annihilated.
    </p>
    <p v-if="infinityAnnihilationUnlocked">
      Infinity Annihilation is unlocked.
    </p>
    <p v-else>
      Infinity Annihilation unlocks after 5 Dimensions are annihilated.
    </p>
    <div class="l-annihilation-dimensions">
      <button
        v-for="dimension in dimensions"
        :key="dimension.tier"
        class="c-annihilation-upgrade"
        :class="{ 'c-annihilation-upgrade--bought': dimension.isAnnihilated }"
        :disabled="!dimension.canAnnihilate"
        @click="annihilateDimension(dimension.tier)"
      >
        <template v-if="dimension.isAnnihilated">
          Dimension {{ dimension.tier }} annihilated
        </template>
        <template v-else>
          Annihilate Dimension {{ dimension.tier }}<br>
          Cost: {{ formatAnnihilation(dimension.cost, 2) }} Matter
        </template>
      </button>
    </div>
    <button
      v-if="infinityAnnihilationUnlocked"
      class="c-annihilation-upgrade"
      :class="{ 'c-annihilation-upgrade--bought': infinityColumnAnnihilated }"
      :disabled="!canAnnihilateInfinityColumn"
      @click="annihilateInfinityColumn"
    >
      <template v-if="infinityColumnAnnihilated">
        First Infinity Upgrade column annihilated
      </template>
      <template v-else>
        Annihilate first Infinity Upgrade column<br>
        Cost: {{ formatAnnihilation(infinityColumnCost, 2) }} Matter
      </template>
    </button>
  </div>
</template>

<style scoped>
.l-annihilation-tab {
  text-align: center;
  color: #aaa;
}

.c-annihilation-tab__amount {
  font-size: 3rem;
  font-weight: bold;
}

.c-annihilation-quotes {
  display: block;
  margin: 0 auto 1rem;
  padding: 0.7rem 1.5rem;
  color: #aaa;
  background: #111;
  border: 0.15rem solid #888;
  border-radius: var(--var-border-radius, 0.5rem);
  cursor: pointer;
}

.c-annihilation-quotes:hover {
  color: #111;
  background: #888;
}

.c-annihilation-reset {
  min-width: 42rem;
  min-height: 6rem;
  color: #aaa;
  background: #111;
  border: 0.25rem solid #888;
  border-radius: var(--var-border-radius, 0.5rem);
  font-family: Typewriter, serif;
  font-size: 1.5rem;
  cursor: pointer;
}

.c-annihilation-reset:disabled {
  opacity: 0.5;
  cursor: default;
}

.c-annihilation-reset:not(:disabled):hover {
  color: #111;
  background: #888;
}

.l-annihilation-dimensions {
  display: grid;
  grid-template-columns: repeat(2, minmax(20rem, 1fr));
  gap: 1rem;
  max-width: 52rem;
  margin: 2rem auto;
}

.c-annihilation-upgrade {
  min-height: 5rem;
  color: #aaa;
  background: #111;
  border: 0.15rem solid #888;
  border-radius: var(--var-border-radius, 0.5rem);
  cursor: pointer;
}

.c-annihilation-upgrade:disabled {
  opacity: 0.5;
  cursor: default;
}

.c-annihilation-upgrade:not(:disabled):hover {
  color: #111;
  background: #888;
}

.c-annihilation-upgrade--bought {
  color: #111;
  background: #888;
  opacity: 1;
}
</style>
