<template>
  <AppNavbar />
  <AppModal
    :title="$root.locale.authorization"
    :visible="authorizationModal.visible"
    @close="closeAuthModal"
  >
    <div class="control">
      <span class="control-title">{{ $root.locale.login }}</span>
      <input
        type="text"
        :placeholder="$root.locale.login"
        v-model="authorizationModal.login"
      />
    </div>

    <div class="control">
      <span class="control-title">{{ $root.locale.password }}</span>
      <input
        type="password"
        :placeholder="$root.locale.password"
        v-model="authorizationModal.password"
      />
    </div>

    <div class="control text-center">
      <a
        class="btn"
        @click="
          login();
          closeAuthModal();
        "
      >
        {{ $root.locale.enter }}
      </a>
    </div>
  </AppModal>

  <div class="container">
    <router-view />
  </div>
</template>

<script>
import AppNavbar from "./components/AppNavbar.vue";
import AppModal from "./components/AppModal.vue";
export default {
  name: "App",
  components: {
    AppNavbar,
    AppModal,
  },
  data() {
    return {
      authorizationModal: {
        login: "",
        password: "",
        visible: false,
      },

      user: {
        name: null,
        role: null,
        token: null,
      },

      locale: {
        authorization: "Авторизация",
        cancelTicket: "Отменить заявку",
        confirmTicket: "Утвердить заявку",
        createTicket: "Создать заявку",
        denyTicket: "Отклонить заявку",
        editTicketTitle: "Редактировать заявку",
        emailTitle: "Электронная почта",
        emailValue: "test@test.test",
        enter: "Войти",
        exit: "Выйти",
        id: "Идентификатор",
        login: "Логин",
        markTicketAsPaid: "Отметить оплаченым",
        notSpecified: "Не указано",
        password: "Пароль",
        phoneNumberTitle: "Телефон",
        phoneNumberValue: "+0 (000) 000-00-00",
        profile: "Профиль",
        profileAvatar: "Аватар профиля",
        role: "Роль",
        roles: {
          INITIATOR: "Сотрудник",
          PURCHASING_MANAGER: "Менеджер по закупкам",
          ACCOUNTING_MANAGER: "Бухгалтер",
        },
        status: {
          REQUESTED: "Запрошено",
          CANCELLED: "Отменено",
          CONFIRMED: "Одобрено",
          DENIED: "Отклонено",
          PAYMENT_COMPLETED: "Обработано",
        },
        ticketTitle: "Описание",
        ticketCount: "Количество",
        ticketDescription: "Комментарии",
        ticketStatus: "Статус",
        ticketPrice: "Цена",
        ticketsList: "Список заявок",
        welcomeContacts: "Свяжитесь с нами:",
        welcomeDescription: "Мы трудимся чтобы ваше время было потрачено с пользой!",
        welcomeTitle: "Добро пожаловать!",
      },
    };
  },
  methods: {
    async openAuthModal() {
      this.authorizationModal.login = "";
      this.authorizationModal.password = "";
      this.authorizationModal.visible = true;
    },

    async closeAuthModal() {
      this.authorizationModal.visible = false;
    },

    async login() {
      const response = await fetch("http://localhost:3000/auth", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          login: this.authorizationModal.login,
          password: this.authorizationModal.password,
        }),
      });
      this.user.token = await response.text();
      localStorage.setItem("token", this.user.token);
      this.fetchUser();
    },

    async logout() {
      this.user = {
        name: null,
        role: null,
        token: null,
      };
      localStorage.removeItem("token");
      this.$router.push("/");
    },

    async fetchUser() {
      const token = this.user.token;
      const response = await fetch("http://localhost:3000/user", {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      });

      if (response.status === 401) {
        this.logout();
        return;
      }

      this.user = await response.json();
      this.user.token = token;
    },

    isAuthorized() {
      return this.user.token !== null;
    },
  },

  async created() {
    this.user.token = localStorage.getItem("token");

    if (this.isAuthorized()) {
      await this.fetchUser();
    } else {
      this.$router.push("/");
    }
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.container {
  margin: 40px auto;
  max-width: 1080px;
}

.control {
  display: flex;
  flex-direction: column;
  text-align: left;
  position: relative;
}

.control.text-center {
  text-align: center;
}

.control input[type="number"],
.control input[type="password"],
.control input[type="text"],
.control textarea {
  padding: 10px;
  margin: 10px 0;
  border: 1px solid #ccc;
  border-radius: 5px;
  width: 100%;
  box-sizing: border-box;
}

.input-error {
  border-color: red !important;
}

.btn {
  display: inline-block;
  padding: 10px 20px;
  margin: 10px;
  border-radius: 5px;
  background-color: #42b983;
  color: white;
  cursor: pointer;
}

.btn.red {
  background-color: #d05555;
}

.btn.disabled {
  background-color: #b0b0b0;
  cursor: not-allowed;
  opacity: 0.6;
}

.modal a {
  text-decoration: none;
  font-size: 14px;
}
</style>
