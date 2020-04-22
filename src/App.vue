<template>
  <div id="app">
    <h1>LuckPerms permission search</h1>
    <div v-if="!permissions">
      <p>Run <code>/lp editor</code> to generate permission data, then use the 10 digit code in the URL here:</p>
      <input type="text" v-model="code">
      <button type="submit" @click="getData">Get data</button>
    </div>
    <div v-else>
      <input type="text" v-model="filter" placeholder="Filter">
      <ul>
        <li v-for="permission in filteredPermissions" :key="`${permission.holder}_${permission.node}`">
          <span class="type">{{ permission.holderType === 'group' ? 'G' : 'U' }}</span>
          <span class="holder">{{ permission.holderType === 'group' ? permission.holder : permission.holderName }}</span>
          <span class="node">{{ permission.node }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      code: '',
      filter: '',
      permissions: null
    }
  },
  computed: {
    filteredPermissions() {
      return this.permissions.filter(permission => permission.node.includes(this.filter));
    }
  },
  methods: {
    async getData() {
      const response = await axios.get(`https://bytebin.lucko.me/${this.code}`);

      const array = [];

      response.data.permissionHolders.forEach(holder => {
        holder.nodes.forEach(node => {
          array.push({
            node: node.key,
            holder: holder.id,
            holderType: holder.type,
            holderName: holder.displayName
          });
        });
      });

      this.permissions = array;
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

ul {
  list-style: none;
  margin: 0 auto;
  padding: 0;
  max-width: 600px;
  text-align: left;
}

li {
  background: lightgray;
  padding: .25rem .5rem;
  margin-bottom: .25rem;
}

.type {
  font-weight: bold;
  padding-right: .5rem;
  display: inline-block;
  border-right: 1px solid rgba(0,0,0,.25);
}

.holder {
  font-weight: bold;
  display: inline-block;
  padding: 0 .5em;
  margin-right: .5rem;
  border-right: 1px solid rgba(0,0,0,.25);
}
</style>
