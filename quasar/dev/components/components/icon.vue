<template>
  <div class="q-layout-padding">
    <div>
      <q-icon :name="icon" class="gigi" style="font-size: 5rem;" @click="clicked" />
      <span style="margin: 0 15px;">{{ icon }}</span>
      <q-icon :name="icon" class="gigi" style="font-size: 5rem;">
        <q-tooltip>{{ icon }}</q-tooltip>
      </q-icon>
    </div>
    <q-option-group
      type="radio"
      v-model="icon"
      :options="[
        {value: 'cloud', label: 'A Material icon'},
        {value: 'mdi-airballoon', label: 'A MDI icon'},
        {value: 'fab fa-github', label: 'A Font Awesome icon'},
        {value: 'ion-airplane', label: 'A Ionicon (platform dependent)'},
        {value: 'ion-md-airplane', label: 'A Ionicon (md)'},
        {value: 'ion-ios-airplane', label: 'A Ionicon (ios)'},
        {value: 'eva-paper-plane-outline', label: 'A Eva icon'}
      ]"
      style="margin-top: 25px"
    />

    <div class="q-mt-xl">
      <div v-for="set in sets" :key="set.setName">
        <h3>{{ set.setName }}</h3>
        <div>
          <q-chip class="q-ma-xs" v-for="theIcon in set.icons" :key="set.setName + theIcon.name + theIcon.val">
            <q-avatar :icon="theIcon.val" color="primary" text-color="white" />
            {{ theIcon.name }}
          </q-chip>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import matSet from '../../../icon-set/material-icons.js'
import mdiSet from '../../../icon-set/mdi-v3.js'
import fontawesomeSet from '../../../icon-set/fontawesome-v5.js'
import ioniconsSet from '../../../icon-set/ionicons-v4.js'
import evaSet from '../../../icon-set/eva-icons.js'
import themifySet from '../../../icon-set/themify.js'

function parseSet (setName, set) {
  const icons = []
  Object.keys(set).forEach(key => {
    const prop = set[key]
    Object.keys(prop).forEach(name => {
      const val = prop[name]
      if (typeof val === 'string') {
        icons.push({ name: `${key}/${name}`, val })
      }
    })
  })
  return {
    setName,
    icons
  }
}

export default {
  data () {
    return {
      icon: 'cloud'
    }
  },
  computed: {
    sets () {
      return [matSet, mdiSet, fontawesomeSet, ioniconsSet, evaSet, themifySet]
        .map(({ name, ...set }) => parseSet(name, set))
    }
  },
  methods: {
    clicked () {
      console.log('clicked')
    }
  }
}
</script>
