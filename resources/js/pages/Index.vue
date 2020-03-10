<template>
  <div class="table-responsive">
    <div class="mb-3">
      <router-link :to="{ name: 'todo.add' }" class="btn btn-primary mb-3">Tambah Data</router-link>
    </div>
    <b-table
      striped
      hover
      :items="todos.data"
      show-empty
      :fields="fields"
      class="d-inline"
      @row-clicked="detailsPage"
    >
      <template v-slot:cell(jadwal)="row">
        <p :class="{ 'coret': row.item.status == 1 }">{{ row.item.name }}</p>
      </template>
      <template v-slot:cell(deskripsi)="row">
        <p :class="{ 'coret': row.item.status == 1 }">{{ row.item.note }}</p>
      </template>
      <template v-slot:cell(tanggal_berakhir)="row">
        <p :class="{ 'coret': row.item.status == 1 }">{{ row.item.due_date }}</p>
      </template>
      <template v-slot:cell(status)="row">
        <span class="badge badge-success" v-if="row.item.status == 1">Selesai</span>
        <span class="badge badge-info" v-else>Aktif</span>
      </template>
      <template v-slot:cell(action)="row">
        <div :key="row.index">
          <button
            class="btn btn-primary btn-sm"
            @click="changeStatus(row.item.id)"
            v-if="row.item.status == 0"
          >Selesai</button>
          <button class="btn btn-danger btn-sm" @click="hapus(row.item.id)">Hapus</button>
        </div>
      </template>
    </b-table>
    <div class="mt-3">
      <b-pagination
        v-model="page"
        :total-rows="todos.total"
        :per-page="todos.per_page"
        aria-controls="my-table"
        v-if="todos.data && todos.data.length > 0"
      ></b-pagination>
    </div>
  </div>
</template>

<style scoped>
.coret {
  text-decoration: line-through;
}
</style>
<script>
import { mapActions, mapState } from "vuex";
export default {
  name: "TodoIndex",
  created() {
    this.getTodos();
  },
  data() {
    return {
      fields: [
        "#",
        "jadwal",
        "deskripsi",
        "tanggal_berakhir",
        "status",
        "created_at",
        "action"
      ]
    };
  },
  watch: {
    page() {
      this.getTodos();
    }
  },
  computed: {
    ...mapState("todo", {
      todos: state => state.todos
    }),
    page: {
      get() {
        return this.$store.state.todo.page;
      },
      set(val) {
        this.$store.state.todo.page = val;
      }
    }
  },
  methods: {
    ...mapActions("todo", ["getTodos", "updateStatus", "hapusTodo"]),
    changeStatus(id) {
      this.$swal({
        title: "Anda Yakin?",
        text: "To do List Anda Telah Selesai",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes!"
      }).then(result => {
        if (result.value) {
          this.updateStatus(id).then(() => {
            this.getTodos();
          });
        }
      });
    },
    hapus(id) {
      this.$swal({
        title: "Anda Yakin Ingin Menghapus Daftar ini?",
        text: "Anda tidak akan dapat merestore ini!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes, Delete it!"
      }).then(result => {
        if (result.value) {
          this.hapusTodo(id).then(() => {
            this.getTodos();
          });
        }
      });
    },
    detailsPage(item) {
      this.$router.push({ name: "todo.detail", params: { id: item.id } });
    }
  }
};
</script>

