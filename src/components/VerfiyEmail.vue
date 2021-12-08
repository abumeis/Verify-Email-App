<template>
  <div class="verify-email__main">
    <h2>This App gives you the status of an entred email</h2>
    <form @submit.prevent="onVerfiy" class="verify-email__form">
      <input
        type="email"
        class="verify-email__input"
        v-model="email"
        placeholder="ex: abumeis1991@gmail.com"
      />
      <button
        type="submit"
        :class="
          disabledButton
            ? 'verify-email__button-disabled'
            : 'verify-email__button'
        "
        :disabled="disabledButton"
      >
        Verify
      </button>
    </form>
    <div v-if="showStatus" class="verify-email__status">
      <h3 v-if="validEmail">Votre email est valide, merci</h3>
      <h3 v-else>Cet email est invalide</h3>
    </div>
    <div v-if="showStatus" class="verify-email__status-info">
      <p><strong>Email:</strong> {{ dispalyStatus.email }}</p>
      <p :class="validEmail ? 'status-valid' : 'status-invalid'">
        <strong style="color: black">Status:</strong> {{ dispalyStatus.status }}
      </p>
    </div>
    <p v-if="error" class="verify-email__error">* {{ errorMessage }}</p>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "VerfiyEmail",
  data() {
    return {
      email: "",
      validEmail: undefined,
      showStatus: false,
      error: false,
      errorMessage: "",
    };
  },

  computed: {
    dispalyStatus() {
      if (this.email === "") return;
      return {
        email: this.email,
        status: this.validEmail ? " valide" : " invalide",
      };
    },
    disabledButton() {
      return this.email === "";
    },
  },
  methods: {
    async onVerfiy() {
      try {
        const url = `https://api.hunter.io/v2/email-verifier?email=${this.email}&api_key=${process.env.APIKEY}`;
        const response = await axios.get(url);
        const result = response.data.data.status;
        if (result === "accept_all" || result === "valid") {
          this.validEmail = true;
          this.showStatus = true;
        } else {
          this.validEmail = false;
          this.showStatus = true;
        }
      } catch (err) {
        this.error = true;
        this.errorMessage = err;
      } finally {
        setTimeout(() => {
          this.email = "";
          this.showStatus = false;
          this.error = false;
        }, 6000);
      }
    },
  },
};
</script>

<style scoped>
.verify-email__main {
  margin-top: 150px;
  margin-right: auto;
  margin-left: auto;
  max-width: 960px;
  padding-right: 10px;
  padding-left: 10px;
}
.verify-email__form {
  display: flex;
  background-color: #fff;
  border-radius: 3px;
  border: 2px solid #0f6e94;
  padding: 30px 10px 30px;
  text-align: left;
}
.verify-email__input {
  background-color: white;
  border: 1px solid #d8edf7;
  opacity: 2;
  padding: 4px;
  border-radius: 4px;
  align-items: center;
  display: flex;
  width: 100%;
  font-size: 1rem;
}
.verify-email__input:focus {
  outline: none;
  box-shadow: 0 1px 2px gray;
}
.verify-email__button {
  width: 180px;
  height: 48px;
  border-radius: 4px;
  font-size: 16px;
  background-color: #0f6e94;
  color: white;
}
.verify-email__button-disabled {
  width: 180px;
  height: 48px;
  border-radius: 4px;
  font-size: 1rem;
  background-color: #848688;
  color: white;
}
p {
  font-size: 1.2rem;
}
.verify-email__status-info {
  display: flex;
  justify-content: space-between;
}
.status-valid {
  color: green;
}
.status-invalid {
  color: red;
}
.verify-email__error {
  color: red;
}
@media (max-width: 800px) {
  .verify-email__button,
  .verify-email__button-disabled {
    height: 37px;
  }
  .verify-email__status-info {
    display: flex;
    flex-direction: column;
  }
  input {
    font-size: 4rem;
  }
  ::placeholder {
    font-size: 0.8rem;
  }
  h2 {
    text-align: center;
  }
}
</style>