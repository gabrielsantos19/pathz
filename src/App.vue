<template>
  <h1>Dijkstra's shortest path algorithm</h1>
  <main>
    <textarea v-model="graphRaw">
    </textarea>

    <div class="painel">
      <h2>Shortest path</h2>
      <div class="form">
        <label for="source">From</label>
        <select name="source" id="source" v-model="source">
          <option v-for="(key, value) in graph"
            :value="value"
            :key="key">
            {{value}}
          </option>
        </select>

        <label for="destiny">to</label>
        <select name="destiny" id="destiny" v-model="destiny">
          <option v-for="(key, value) in graph"
            :value="value"
            :key="key">
            {{value}}
          </option>
        </select>

        <button @click="dijkstra(graph, source, destiny)">Find</button>
      </div>

      <span>Total: {{total}}</span>
      <div class="path">
        <div v-for="v in path" :key="v">
          {{v}}
        </div>
      </div>
    </div>
  </main>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Heap from 'heap-js';

const graphs = [
`{
"a": {
  "b": 60,
  "c": 54,
  "d": 42
},

"b": {
  "d": 71,
  "f": 29
},

"c": {
  "d": 56,
  "e": 67
},

"d": {
  "e": 26,
  "f": 52,
  "g": 87
},

"e": {
  "g": 70,
  "i": 73
},

"f": {
  "g": 20,
  "h": 25
},

"g": {
  "h": 36,
  "i": 59,
  "j": 32
},

"h": {
  "j": 25
},

"i": {
  "j": 26
},

"j": {}
}`,
`{
"1": {
  "2": 7,
  "3": 9,
  "6": 14
},

"2": {
  "1": 7,
  "3": 10,
  "4": 15
},

"3": {
  "1": 9,
  "2": 10,
  "4": 11,
  "6": 2
},

"4": {
  "2": 15,
  "3": 11,
  "5": 6
},

"5": {
  "4": 6,
  "6": 9
},

"6": {
  "1": 14,
  "3": 2,
  "5": 9
}
}`
]
const defaultGraphRaw = graphs[1]

export default defineComponent({
  name: 'App',
  components: {
  },
  data() {
    return {
      graphRaw: defaultGraphRaw,
      source: '',
      destiny: '',
      path: [],
      total: null,
    };
  },
  computed: {
    graph() {
      let g = {};
      try {
        const t = JSON.parse(this.graphRaw);
        g = t;
      } catch {
        g = {};
      }
      console.debug(g)
      return g;
    },
  },
  methods: {
    dijkstra(graph, source, destiny) {
      const copy = {...graph}

      const priority = (a, b) => a[0] - b[0]
      const heap = new Heap(priority);
      const table = {};
      for (const vertex in copy) {
        table[vertex] = [Infinity, null, true];
      }
      table[source] = [0, null, false]
      heap.push([0, null, source])

      while (heap.length > 0) {
        const min = heap.pop()
        const edges = copy[min[2]]

        table[min[2]][2] = false

        for (const edge in edges) {
          const distance = min[0] + copy[min[2]][edge]

          heap.push([distance, min[2], edge])
          
          if (table[edge][2] && distance < table[edge][0]) {
            table[edge] = [distance, min[2], true]
          }
        }

        copy[min[2]] = []
      }

      if (table[destiny][1]) {
        let vertex = destiny
        const stack = []
        while (vertex != null) {
          stack.push(vertex)
          vertex = table[vertex][1]
        }
        stack.reverse()
        this.path = stack
        this.total = table[destiny][0]
      } else {
        this.path = []
        this.total = 0
      }
    },
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;

  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 20px;

  display: flex;
  flex-flow: column nowrap;
}
main {
  display: flex;
  flex-flow: row nowrap;
  flex-grow: 1;
}
textarea {
  min-width: 400px;
  max-width: 400px;
  font-size: 18px;
  margin-bottom: 20px;
}
.painel {
  flex-grow: 1;
  display: flex;
  flex-flow: column nowrap;
  align-items: center;
  justify-content: center;
}
.form {
  display: flex;
  flex-flow: row wrap;
  font-size: 20px;
  align-items: center;
  margin-bottom: 50px;
}
.form select {
  height: 33px;
  width: 220px;
  font-size: 20px;
  margin: 0px 10px;
}
.form button {
  font-size: 16px;
  height: 33px;
}
.path {
  display: flex;
  flex-flow: row wrap;
}
.path div {
  display: flex;
  flex-flow: row nowrap;
  justify-content: center;
  border-radius: 10px;
  font-size: 25px;
  min-width: 25px;
  margin: 10px;
  padding: 15px;
  background-color: royalblue;
  color: white;
}
</style>
