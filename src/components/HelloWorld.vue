<template>
  <b-container fluid>
    <div>
      <div class="mb-2">
        <b-form-checkbox v-model="stickyHeader" inline
          >Sticky header</b-form-checkbox
        >
        <b-form-checkbox v-model="noCollapse" inline
          >No border collapse</b-form-checkbox
        >
      </div>
      <b-table
        sticky-header="stickyHeader"
        :no-border-collapse="noCollapse"
        responsive
        :items="filtered"
        :fields="fields"
        split="split"
      >
        <!-- We are using utility class `text-nowrap` to help illustrate horizontal scrolling -->
        <template #head(id)="scope">
          <div class="text-nowrap">Row ID</div>
          <b-form-input
            v-model="filters.id"
            type="search"
            :placeholder="scope.label"
          ></b-form-input>
        </template>
        <template #head()="scope">
          <div class="text-nowrap">Heading {{ scope.label }}</div>
          <b-form-input
            v-model="filters[scope.column]"
            :placeholder="scope.label"
            type="search"
          ></b-form-input>
        </template>
      </b-table>
    </div>
  </b-container>
</template>

<script>
import Sortable from "sortablejs";
export default {
  name: "HelloWorld",
  data() {
    return {
      stickyHeader: true,
      noCollapse: false,
      fields: [
        {
          key: "id",
          stickyColumn: true,
          isRowHeader: true,
          variant: "primary",
          sortable: true,
          filterByFormatted: true
        },
        { key: "a", sortable: true, filterByFormatted: true },
        { key: "b", sortable: true, filterByFormatted: true },
        { key: "c", sortable: true, filterByFormatted: true },
        { key: "d", sortable: true, filterByFormatted: true },
        { key: "e", sortable: true, filterByFormatted: true },
        { key: "f", sortable: true, filterByFormatted: true },
        { key: "g", sortable: true, filterByFormatted: true },
        { key: "h", sortable: true, filterByFormatted: true },
        { key: "i", sortable: true, filterByFormatted: true },
        { key: "j", sortable: true, filterByFormatted: true },
        { key: "k", sortable: true, filterByFormatted: true },
        { key: "l", sortable: true, filterByFormatted: true }
      ],
      items: Array.from({ length: 50 }).map((v, k) => ({
        id: k + 1,
        a: Math.floor(Math.random() * 10000),
        b: Math.floor(Math.random() * 10000),
        c: Math.floor(Math.random() * 10000),
        d: Math.floor(Math.random() * 10000),
        e: Math.floor(Math.random() * 10000),
        f: Math.floor(Math.random() * 10000),
        g: Math.floor(Math.random() * 10000),
        h: Math.floor(Math.random() * 10000),
        i: Math.floor(Math.random() * 10000),
        j: Math.floor(Math.random() * 10000),
        k: Math.floor(Math.random() * 10000),
        l: Math.floor(Math.random() * 10000)
      })),
      filters: {
        id: "",
        a: "",
        b: "",
        c: "",
        d: "",
        e: "",
        f: "",
        g: "",
        h: "",
        i: "",
        j: "",
        k: "",
        l: ""
      }
    };
  },
  computed: {
    filtered() {
      const filtered = this.items.filter(item => {
        return Object.keys(this.filters).every(key =>
          String(item[key]).includes(this.filters[key])
        );
      });
      return filtered.length > 0
        ? filtered
        : [
            {
              id: "",
              a: "",
              b: "",
              c: "",
              d: "",
              e: "",
              f: "",
              g: "",
              h: "",
              i: "",
              j: "",
              k: "",
              l: ""
            }
          ];
    }
  },
  methods: {},
  directives: {
    draggable: {
      inserted: function(el, binding, a) {
        Sortable.create(el, {
          draggable: ".draggable",
          onEnd: function(e) {
            /* vnode.context is the context vue instance: "This is not documented as it's not encouraged to manipulate the vm from directives in Vue 2.0 - instead, directives should be used for low-level DOM manipulation, and higher-level stuff should be solved with components instead. But you can do this if some usecase needs this. */
            // fixme: can this be reworked to use a component?
            // https://github.com/vuejs/vue/issues/4065
            // https://forum.vuejs.org/t/how-can-i-access-the-vm-from-a-custom-directive-in-2-0/2548/3
            // https://github.com/vuejs/vue/issues/2873 "directive interface change"
            // `binding.expression` should be the name of your array from vm.data
            // set the expression like v-draggable="items"
            var clonedItems = a.context[binding.expression].filter(function(
              item
            ) {
              return item;
            });
            clonedItems.splice(
              e.newIndex,
              0,
              clonedItems.splice(e.oldIndex, 1)[0]
            );
            a.context[binding.expression] = [];
            this.nextTick(function() {
              a.context[binding.expression] = clonedItems;
            });
          }
        });
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.draggable {
  cursor: move;
}
</style>
