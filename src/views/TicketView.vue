<template>
  <div>
    <h1>{{ ticket.id }}</h1>
    <div class="ticket-view-i">
      <table>
        <tr>
          <td>{{ $root.locale.ticketTitle }}</td>
          <td>
            {{ ticket.title }}
            <span v-if="$root.user.role == 'INITIATOR'">
              <a class="fa fa-pencil edit" @click="openTitleEditModal"></a>
            </span>
          </td>
        </tr>

        <tr>
          <td>{{ $root.locale.ticketStatus }}</td>
          <td>{{ $root.locale.status[ticket.status] }}</td>
        </tr>

        <tr>
          <td>{{ $root.locale.ticketCount }}</td>
          <td>{{ ticket.count }}</td>
        </tr>

        <tr>
          <td>{{ $root.locale.ticketPrice }}</td>
          <td>
            {{ ticket.price || $root.locale.notSpecified }}
            <span v-if="ticket.price > 0">&#8381;</span>
          </td>
        </tr>

        <tr>
          <td>{{ $root.locale.roles.INITIATOR }}</td>
          <td>{{ ticket.initiator.name }}</td>
        </tr>

        <tr>
          <td>{{ $root.locale.roles.PURCHASING_MANAGER }}</td>
          <td>{{ ticket.purchasingManager?.name || $root.locale.notSpecified }}</td>
        </tr>

        <tr>
          <td>{{ $root.locale.roles.ACCOUNTING_MANAGER }}</td>
          <td>{{ ticket.accountingManager?.name || $root.locale.notSpecified }}</td>
        </tr>
      </table>

      <div class="control text-center description">
        <span class="control-title">{{ $root.locale.ticketDescription }}</span>
        <textarea
          :value="ticket.description"
          @input="editTicket('editDescription', { description: $event.target.value })"
        />
      </div>

      <template v-if="showButtons">
        <template v-if="ticket.status == 'REQUESTED'">
          <div
            class="box-control"
            v-if="$root.user.role == 'INITIATOR' && $root.user.id == ticket.initiator.id"
          >
            <a class="btn red" @click="editTicket('cancel')">{{
              $root.locale.cancelTicket
            }}</a>
          </div>

          <div class="box-control" v-if="$root.user.role == 'PURCHASING_MANAGER'">
            <a class="btn" @click="openConfirmationModal">{{
              $root.locale.confirmTicket
            }}</a>
            <a class="btn red" @click="editTicket('deny')">{{
              $root.locale.denyTicket
            }}</a>
          </div>
        </template>
        <template v-if="ticket.status == 'CONFIRMED'">
          <div class="box-control" v-if="$root.user.role == 'ACCOUNTING_MANAGER'">
            <a class="btn" @click="editTicket('markAsPaid')">{{
              $root.locale.markTicketAsPaid
            }}</a>
          </div>
        </template>
      </template>

      <AppModal
        :title="$root.locale.editTicketTitle"
        :visible="titleEditModal.visible"
        @close="closeTitleEditModal"
      >
        <div class="control">
          <span class="control-title">{{ $root.locale.ticketTitle }}</span>
          <input
            type="text"
            :placeholder="$root.locale.ticketTitle"
            :class="{ 'input-error': titleEditModal.titleError }"
            @input="validateTitle"
            v-model="titleEditModal.title"
          />
        </div>

        <div class="control text-center">
          <a class="btn" @click="editTitle">
            {{ $root.locale.editTicketTitle }}
          </a>
        </div>
      </AppModal>

      <AppModal
        :title="$root.locale.confirmTicket"
        :visible="confirmationModal.visible"
        @close="closeConfirmationModal"
      >
        <div class="control">
          <span class="control-title">{{ $root.locale.ticketPrice }}</span>
          <input
            type="text"
            placeholder="0"
            :class="{ 'input-error': confirmationModal.priceError }"
            @input="validatePrice"
          />
        </div>

        <div class="control text-center">
          <a class="btn" @click="confirmTicket">
            {{ $root.locale.confirmTicket }}
          </a>
        </div>
      </AppModal>
    </div>
  </div>
</template>

<script>
import AppModal from "@/components/AppModal.vue";

export default {
  name: "TicketView",
  components: {
    AppModal,
  },
  data() {
    return {
      confirmationModal: {
        price: 0,
        priceError: true,
        visible: false,
      },
      showButtons: true,
      ticket: {
        id: "00000000-0000-0000-0000-000000000000",
        title: "ERROR",
        status: "ERROR",
        count: 0,
        price: 0,
        initiator: {
          id: "00000000-0000-0000-0000-000000000000",
          name: "ERROR",
          role: "ERROR",
        },
        purchasingManager: null,
        accountingManager: null,
        description: "",
      },
      titleEditModal: {
        title: "",
        titleError: true,
        visible: false,
      },
    };
  },
  methods: {
    openConfirmationModal() {
      this.confirmationModal.price = 0;
      this.confirmationModal.priceError = true;
      this.confirmationModal.visible = true;
    },

    closeConfirmationModal() {
      this.confirmationModal.visible = false;
    },

    openTitleEditModal() {
      this.titleEditModal.title = this.ticket.title;
      this.titleEditModal.titleError = false;
      this.titleEditModal.visible = true;
    },

    closeTitleEditModal() {
      this.titleEditModal.visible = false;
    },

    updateButtons() {
      const { status } = this.ticket;
      this.showButtons =
        status != "CANCELLED" && status != "DENIED" && status != "PAYMENT_COMPLETED";
    },

    async fetchTicket() {
      const root = this.$root;

      const response = await fetch(
        `http://localhost:3000/ticket/${this.$route.params.id}`,
        {
          headers: {
            Authorization: `Bearer ${root.user.token}`,
          },
        }
      );

      if (response.status == 401) {
        root.logout();
      }

      this.ticket = await response.json();
      this.updateButtons();
    },

    async editTicket(op, data = null) {
      const root = this.$root;

      const response = await fetch(
        `http://localhost:3000/ticket/${this.$route.params.id}`,
        {
          method: "PATCH",
          headers: {
            Authorization: `Bearer ${root.user.token}`,
            "Content-Type": "application/json",
          },
          body: JSON.stringify({ op: op, data: data }),
        }
      );

      if (response.status == 401) {
        root.logout();
      }

      this.ticket = await response.json();
      this.updateButtons();
    },

    validateTitle() {
      this.titleEditModal.titleError = this.titleEditModal.title.length < 1;
    },

    validatePrice(event) {
      this.confirmationModal.price =
        parseInt(event.target.value.replace(/\D/g, ""), 10) || 0;
      this.confirmationModal.priceError = this.confirmationModal.price < 1;
    },

    async confirmTicket() {
      if (this.confirmationModal.priceError) {
        return;
      }

      await this.editTicket("confirm", { price: this.confirmationModal.price });
      this.closeConfirmationModal();
    },

    async editTitle() {
      if (this.titleEditModal.titleError) {
        return;
      }

      await this.editTicket("editTitle", { title: this.titleEditModal.title });
      this.closeTitleEditModal();
    },
  },

  async mounted() {
    await this.fetchTicket();
  },
};
</script>

<style scoped>
.description {
  margin-top: 32px;
}

.description textarea {
  width: 100%;
  height: 100px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.ticket-view-i {
  display: flex;
  flex-direction: column;
  margin: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.ticket-view-i table {
  width: 100%;
  border-collapse: collapse;
}

.ticket-view-i table tr td:nth-child(1) {
  font-weight: bold;
  width: 20%;
}

.ticket-view-i table td {
  padding: 5px;
  border: 1px solid #ccc;
  text-align: left;
}

.edit {
  margin-left: 10px;
  cursor: pointer;
}
</style>
