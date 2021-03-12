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
        sticky-header="80vh"
        :no-border-collapse="noCollapse"
        responsive
        :items="filtered"
        :fields="fields"
        ref="my-table"
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
      <b-overlay :show="isBusy" no-wrap opacity="0.5"></b-overlay>
    </div>
  </b-container>
</template>

<script>
const database = [];

for (let i = 0; i < 1000; i++) {
  database.push({
    id: i + 1,
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
  });
}
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
        {
          key: "a",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "b",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "c",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "d",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "e",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "f",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "g",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "h",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "i",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "j",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "k",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        },
        {
          key: "l",
          sortable: true,
          filterByFormatted: true,
          thClass: "draggable"
        }
      ],
      items: [],
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
      },
      currentPage: 0,
      perPage: 20,
      totalItems: database.length,
      isBusy: false
    };
  },
  created() {
    this.fetchItems();
  },
  mounted() {
    const tableScrollBody = this.$refs["my-table"].$el;
    /* Consider debouncing the event call */
    tableScrollBody.addEventListener("scroll", this.onScroll);
  },
  beforeDestroy() {
    /* Clean up just to be sure */
    const tableScrollBody = this.$refs["my-table"].$el;
    tableScrollBody.removeEventListener("scroll", this.onScroll);
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
  methods: {
    async fetchItems() {
      /* No need to call if all items retrieved */
      if (this.items.length === this.totalItems) return;

      /* Enable busy state */
      this.isBusy = true;

      /* Missing error handling if call fails */
      const startIndex = this.currentPage++ * this.perPage;
      const endIndex = startIndex + this.perPage;
      const newItems = await this.callDatabase(startIndex, endIndex);

      /* Add new items to existing ones */
      this.items = this.items.concat(newItems);

      /* Disable busy state */
      this.isBusy = false;
    },
    callDatabase(startIndex, endIndex) {
      return new Promise(resolve => {
        const delay = Math.floor(Math.random() * 1250) + 250;
        setTimeout(() => {
          resolve(database.slice(startIndex, endIndex));
        }, delay);
      });
    },
    onScroll(event) {
      if (
        event.target.scrollTop + event.target.clientHeight >=
        event.target.scrollHeight
      ) {
        if (!this.isBusy) {
          this.fetchItems();
        }
      }
    }
  },
  watch: {
    /* Optionally hide scrollbar when loading */
    isBusy(newVal, oldVal) {
      if (newVal !== oldVal) {
        const tableScrollBody = this.$refs["my-table"].$el;
        if (newVal === true) {
          tableScrollBody.classList.add("overflow-hidden");
        } else {
          tableScrollBody.classList.remove("overflow-hidden");
        }
      }
    }
  },
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
body {
  padding: 1rem;
}
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
