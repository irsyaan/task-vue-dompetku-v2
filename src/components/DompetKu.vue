<template>
  <v-app>
    <v-container>
      <v-card class="pa-5" outlined>
        <v-card-title class="text-h4">Dompetku v2 – Expense & Income Money Management</v-card-title>
        <v-divider></v-divider>

        <!-- Form for new entry -->
        <v-form @submit.prevent="addEntry">
          <v-text-field v-model="newEntry.description" label="Keterangan" outlined required></v-text-field>
          <v-text-field v-model.number="newEntry.amount" label="Jumlah Uang" type="number" outlined required></v-text-field>
          <v-select
            v-model="newEntry.category"
            :items="categories"
            label="Kategori"
            outlined
            required
          ></v-select>
          <v-btn type="submit" color="primary" class="mt-3">Submit</v-btn>
        </v-form>

        <!-- Summary -->
        <v-card-subtitle class="mt-5">
          <strong>Summary:</strong><br>
          Total Entries: {{ entries.length }}<br>
          Total Income: Rp {{ totalIncome }}<br>
          Total Expense: Rp {{ totalExpense }}<br>
          Balance: Rp {{ balance }}
        </v-card-subtitle>

        <!-- List of entries -->
        <v-list class="mt-5">
          <v-subheader>Rekap Keuangan</v-subheader>
          <v-alert v-if="entries.length === 0" type="info">Belum ada data.</v-alert>
          <v-list-item
            v-for="(entry, index) in entries"
            :key="index"
            class="mb-2"
          >
            <v-list-item-content>
              <v-list-item-title>{{ entry.description }} - Rp {{ entry.amount }} ({{ entry.category }})</v-list-item-title>
            </v-list-item-content>
            <v-list-item-action>
              <v-btn icon color="red" @click="removeEntry(index)">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </v-list-item-action>
          </v-list-item>
        </v-list>

        <!-- Clear all entries button -->
        <v-btn color="error" class="mt-5" @click="confirmClearAll">
          Hapus Semua Data
        </v-btn>
      </v-card>
    </v-container>
  </v-app>
</template>

<script>
import { ref, computed, onMounted } from 'vue';
import { loadEntries, saveEntries, clearEntries } from '../stores/store.js'; // Import the storage functions

export default {
  data() {
    return {
      newEntry: {
        description: "",
        amount: null,
        category: "",
      },
      categories: ["Food", "Transport", "Shopping", "Other"],
      entries: loadEntries()  // Load entries from storage on initialization
    };
  },
  computed: {
    totalIncome() {
      return this.entries
        .filter(entry => entry.amount > 0)
        .reduce((sum, entry) => sum + entry.amount, 0);
    },
    totalExpense() {
      return this.entries
        .filter(entry => entry.amount < 0)
        .reduce((sum, entry) => sum + Math.abs(entry.amount), 0);
    },
    balance() {
      return this.totalIncome - this.totalExpense;
    }
  },
  methods: {
    addEntry() {
      if (this.newEntry.description && this.newEntry.amount && this.newEntry.category) {
        this.entries.push({ ...this.newEntry });
        saveEntries(this.entries);  // Save entries after adding a new one
        this.newEntry = { description: "", amount: null, category: "" };
      } else {
        alert("Harap isi keterangan, jumlah uang, dan kategori.");
      }
    },
    removeEntry(index) {
      this.entries.splice(index, 1);
      saveEntries(this.entries);  // Save entries after removing one
    },
    confirmClearAll() {
      if (confirm("Apakah Anda yakin ingin menghapus semua data?")) {
        this.clearAllEntries();
      }
    },
    clearAllEntries() {
      this.entries = [];
      clearEntries();  // Use the clearEntries function
    }
  },
  onMounted() {
    this.entries = loadEntries();  // Load entries when component is mounted
  }
};
</script>

<style>
@import "../../node_modules/vuetify/dist/vuetify.min.css";
/* Background and container styles */
/*body {
  background: #f0f4f8;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.container {
  max-width: 450px;
  background: #ffffff;
  padding: 30px 20px;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  text-align: center;
  color: #333;
  transition: box-shadow 0.3s ease;
}

.container:hover {
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

/* Form styles */
/*.form {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 20px;
}

.input {
  padding: 12px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  transition: border-color 0.3s ease;
}

.input:focus {
  border-color: #4caf50;
  outline: none;
}

.btn {
  padding: 12px;
  background-color: #4caf50;
  color: white;
  font-weight: bold;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn:hover {
  background-color: #43a047;
}

/* List styles */
/*.list {
  margin-top: 25px;
}

.entry-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 15px;
  background: #f9fafb;
  border: 1px solid #e0e4e7;
  border-radius: 8px;
  margin-bottom: 10px;
  transition: background-color 0.2s ease;
}

.entry-item:hover {
  background-color: #e8f5e9;
}

.entry-item span {
  font-size: 16px;
  color: #444;
}

.delete-btn {
  background-color: #ef5350;
  border: none;
  color: white;
  font-size: 14px;
  padding: 6px 10px;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.delete-btn:hover {
  background-color: #e53935;
}

.clear-btn {
  margin-top: 25px;
  background-color: #ff7043;
  color: white;
  border: none;
  font-weight: bold;
  padding: 12px;
  width: 100%;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.clear-btn:hover {
  background-color: #f4511e;
}

/* Heading styles */
/*h1 {
  font-size: 24px;
  margin-bottom: 20px;
  color: #4caf50;
}

h2 {
  font-size: 20px;
  color: #444;
  margin-top: 20px;
  margin-bottom: 15px;
}*/
</style>
