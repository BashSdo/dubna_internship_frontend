<template>
  <div class="tickets-view">
    <div class="tickets-hero">
      <p class="hero-title">{{ $root.locale.ticketsList }}</p>

      <template v-if="$root.user.role === 'INITIATOR'">
        <a @click="newTicketModal.visible = true" class="btn">
          {{ $root.locale.createTicket }}
        </a>

        <AppModal
          :title="$root.locale.createTicket"
          :visible="newTicketModal.visible"
          @close="newTicketModal.visible = false"
        >
          <div class="control">
            <label>{{ $root.locale.ticketTitle }}</label>
            <input
              type="text"
              :placeholder="$root.locale.ticketTitle"
              :class="{ 'input-error': newTicketModal.titleError }"
              @input="validateTitle"
            />
          </div>

          <div class="control">
            <label>{{ $root.locale.ticketCount }}</label>
            <input
              type="text"
              placeholder="1"
              :class="{ 'input-error': newTicketModal.countError }"
              @input="validateCount"
            />
          </div>

          <div class="control">
            <label>{{ $root.locale.ticketDescription }}</label>
            <textarea
              :placeholder="$root.locale.ticketDescription"
              v-model="newTicketModal.description"
            />
          </div>

          <div class="control text-center">
            <a
              :class="{
                btn: true,
                disabled: newTicketModal.titleError || newTicketModal.countError,
              }"
              @click="createTicket"
            >
              {{ $root.locale.createTicket }}
            </a>
          </div>
        </AppModal>
      </template>
    </div>

    <div class="tickets-list">
      <router-link
        class="ticket"
        v-for="ticket in pagination.tickets"
        :to="{ name: 'TicketView', params: { id: ticket.id } }"
        :key="ticket.id"
      >
        <p class="ticket-title">
          {{ ticket.title }}
          <span class="tag">{{ $root.locale.status[ticket.status] }}</span>
        </p>
        <span class="ticket-id">{{ ticket.id }}</span>

        <p class="ticket-author"><i class="fas fa-user"></i>{{ ticket.initiator.name }}</p>
      </router-link>
    </div>

    <div class="pagination">
      <a
        class="btn btn-page"
        v-for="i in pagination.totalPages"
        :key="i"
        @click="setPage(i)"
      >
        {{ i }}
      </a>
    </div>
  </div>
</template>

<script>
import AppModal from "@/components/AppModal.vue";
export default {
  name: "TicketsView",
  components: {
    AppModal,
  },
  data() {
    return {
      pagination: {
        tickets: [],
        totalTickets: 0,
        page: 0,
        totalPages: 0,
        offset: 0,
        limit: 100,
      },

      newTicketModal: {
        title: "",
        titleError: false,
        description: "",
        count: 0,
        countError: false,
        visible: false,
      },
    };
  },
  methods: {
    async setPage(page) {
      const pagination = this.pagination;
      pagination.page = page;
      pagination.offset = (pagination.page - 1) * pagination.limit;
      await this.fetchTickets();
    },

    async fetchTickets() {
      const root = this.$root;
      const pagination = this.pagination;

      pagination.tickets = [];

      const response = await fetch(
        `http://localhost:3000/ticket?offset=${pagination.offset}&limit=${pagination.limit}`,
        {
          headers: {
            Authorization: `Bearer ${root.user.token}`,
          },
        }
      );

      if (response.status === 401) {
        root.logout();
        return;
      }

      const { tickets, totalCount } = await response.json();
      pagination.tickets = tickets;
      pagination.totalTickets = totalCount;
      pagination.totalPages = Math.ceil(pagination.totalTickets / pagination.limit);
    },

    async createTicket() {
      const root = this.$root;

      if (this.newTicketModal.titleError || this.newTicketModal.countError) {
        return;
      }

      const response = await fetch(`http://localhost:3000/ticket`, {
        method: "POST",
        headers: {
          Authorization: `Bearer ${root.user.token}`,
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          title: this.newTicketModal.title,
          description: this.newTicketModal.description,
          count: this.newTicketModal.count,
        }),
      });

      const { id } = await response.json();
      this.newTicketModal.visible = false;
      this.$router.push({ name: "TicketView", params: { id } });
    },

    validateTitle(event) {
      this.newTicketModal.title = event.target.value;
      this.newTicketModal.titleError = this.newTicketModal.title.length < 1;
    },

    validateCount(event) {
      this.newTicketModal.count =
        parseInt(event.target.value.replace(/\D/g, ""), 10) || 0;
      this.newTicketModal.countError = this.newTicketModal.count < 1;
    },
  },

  async mounted() {
    this.newTicketModal.title = "";
    this.newTicketModal.titleError = true;

    this.newTicketModal.count = 0;
    this.newTicketModal.countError = true;

    await this.fetchTickets();
  },
};
</script>

<style scoped>
.tickets-view {
  padding-bottom: 100px;
}
.tickets-hero {
  text-align: left;
}

.hero-title {
  font-size: 24px;
  font-weight: 700;
  margin-bottom: 20px;
}

.ticket {
  display: block;
  text-decoration: none;
  text-align: left;

  background-color: #f9f9f9;
  border-radius: 12px;
  margin: 10px auto;
  padding: 10px 20px;
  color: #333;
}

.ticket-id {
  font-size: 14px;
  margin-bottom: 5px;
}

.ticket-title {
  font-size: 18px;
  font-weight: 700;
  margin-bottom: 5px;
}

.ticket-author {
  font-size: 14px;
  color: #666;
}

.ticket-author i {
  margin-right: 5px;
}

.pagination {
  position: sticky;
  display: inline-flex;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.05);
  backdrop-filter: blur(10px);
  border-radius: 12px;
  padding: 10px;
}

.tag {
  background-color: #eee;
  color: #333;
  padding: 5px 10px;
  border-radius: 12px;
  margin-left: 10px;
  font-size: 14px;
}
</style>
