<template>
  <header>
    <nav class="navbar fixed-top navbar-expand-lg navbar-dark bg-dark">
      <div class="container-fluid user-select-none">
        <router-link to="/" class="navbar-brand mb-0 h1">
          <img style="height: 28px" src="../assets/brand.png" alt="XIV ToDo branding" />
        </router-link>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div
          v-if="this.$route.name != 'Character'"
          class="collapse navbar-collapse"
          id="navbarSupportedContent"
        >
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item">
              <router-link to="/" class="nav-link" @click="collapseNav">
                {{ $t("page.home") }}
              </router-link>
            </li>
            <li v-if="this.$store.getters.hasCharacter" class="nav-item">
              <router-link to="/profile" class="nav-link" @click="collapseNav">
                {{ $t("page.profile") }}
              </router-link>
            </li>
            <li class="nav-item">
              <router-link to="/encounters" class="nav-link" @click="collapseNav">
                {{ $t("page.encounters") }}
              </router-link>
            </li>
            <li class="nav-item">
              <router-link to="/questlines" class="nav-link" @click="collapseNav">
                {{ $t("page.questlines") }}
              </router-link>
            </li>
            <li class="nav-item">
              <router-link to="/challenges" class="nav-link" @click="collapseNav">
                {{ $t("page.challenges") }}
              </router-link>
            </li>
            <li class="nav-item">
              <router-link to="/checklist" class="nav-link" @click="collapseNav">
                {{ $t("page.checklist") }}
              </router-link>
            </li>
          </ul>
          <ul class="navbar-nav mb-2 mb-lg-0">
            <li class="nav-item d-none d-lg-inline">
              <a
                class="nav-link"
                type="button"
                data-bs-toggle="offcanvas"
                data-bs-target="#offcanvasWithBackdrop"
                aria-controls="offcanvasWithBackdrop"
                @click="seenLatestNews()"
              >
                <span class="bi bi-bell position-relative">
                  <span
                    v-if="this.$store.getters.latestNewsSeen < news.latestID"
                    class="
                      position-absolute
                      top-0
                      start-100
                      translate-middle
                      p-1
                      bg-success
                      border border-light
                      rounded-circle
                    "
                  >
                  </span>
                </span>
              </a>
            </li>
            <li v-if="!this.$store.getters.hasCharacter" class="nav-item">
              <router-link to="/settings" class="nav-link" @click="collapseNav">
                {{ $t("page.settings") }}
              </router-link>
            </li>
            <li v-else class="nav-item dropdown">
              <a
                class="nav-link dropdown-toggle"
                href="#"
                id="navbarDropdown"
                role="button"
                data-bs-toggle="dropdown"
                aria-expanded="false"
              >
                {{ this.$store.getters.characterData.Character.Name }}
              </a>
              <ul
                class="dropdown-menu dropdown-menu-end dropdown-menu-dark"
                aria-labelledby="navbarDropdown"
              >
                <li>
                  <h6 class="dropdown-header">{{ $t("message.changeActiveCharacter") }}</h6>
                </li>
                <li v-for="(item, i) of this.$store.state.characters" :key="item.ID">
                  <span
                    v-if="i == this.$store.state.activeCharacterID"
                    class="dropdown-item active"
                  >
                    {{ item.characterData.Character.Name }} <span class="bi-check" />
                  </span>
                  <a v-else class="dropdown-item" href="#" @click="changeActiveCharacter(i)">
                    {{ item.characterData.Character.Name }}
                  </a>
                </li>
                <li><hr class="dropdown-divider" /></li>
                <li>
                  <router-link to="/settings" class="dropdown-item" @click="collapseNav">
                    <span class="bi-gear" /> {{ $t("page.settings") }}
                  </router-link>
                </li>
              </ul>
            </li>
            <img
              v-if="this.$store.getters.hasCharacter"
              class="avatar-nav d-none d-lg-inline"
              :src="this.$store.getters.character.Avatar"
              alt="Portrait of your character"
            />
          </ul>
        </div>

        <div v-else class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="nav navbar-nav me-auto mb-2 mb-lg-0" id="nav-tab" role="tablist">
            <li class="nav-item">
              <a
                class="nav-link active"
                id="nav-profile-tab"
                data-bs-toggle="tab"
                data-bs-target="#nav-profile"
                type="button"
                role="tab"
                @click="collapseNav"
              >
                Profile
              </a>
            </li>
            <li class="nav-item">
              <a
                class="nav-link"
                id="nav-duties-tab"
                data-bs-toggle="tab"
                data-bs-target="#nav-duties"
                type="button"
                role="tab"
                @click="collapseNav"
              >
                Encounters
              </a>
            </li>
            <li class="nav-item">
              <a
                class="nav-link"
                id="nav-questlines-tab"
                data-bs-toggle="tab"
                data-bs-target="#nav-questlines"
                type="button"
                role="tab"
                @click="collapseNav"
              >
                Questlines
              </a>
            </li>
          </ul>
        </div>
      </div>
    </nav>
  </header>

  <div class="offcanvas offcanvas-start" tabindex="-1" id="offcanvasWithBackdrop">
    <div class="offcanvas-header">
      <h5 class="offcanvas-title" id="offcanvasWithBackdropLabel">XIV ToDo - Updates</h5>
      <button type="button" class="btn-close text-reset" data-bs-dismiss="offcanvas"></button>
    </div>
    <div class="offcanvas-body">
      <div v-for="item in news.news" :key="item.ID">
        <h3>{{ item.title }}</h3>
        <small class="text-muted">
          Posted on <b>{{ item.published }}</b>
          <span
            v-if="this.$store.getters.latestNewsSeenPrevious < item.ID"
            class="badge bg-success"
          >
            New
          </span>
        </small>
        <br />
        <p v-html="item.content"></p>
        <br />
      </div>
    </div>
  </div>
</template>

<style lang="scss">
.navbar-brand {
  font-family: "Pacifico", cursive;
}

nav {
  margin-bottom: 10px;

  .nav-link.router-link-exact-active {
    color: #41b883 !important;
    text-decoration: underline;
    text-decoration-thickness: 2px;
    text-underline-offset: 7px;
  }
}

.night nav {
  border-bottom: 1px solid #333333;
}

span.nav-link:hover {
  color: rgba(255, 255, 255, 0.75) !important;
  text-decoration: none !important;
}

.navbar-dark .navbar-nav .nav-link {
  color: rgba(255, 255, 255, 0.75);
}

.navbar-dark .navbar-nav .nav-link:hover {
  color: rgba(255, 255, 255, 1);
  text-decoration: underline;
  text-decoration-thickness: 2px;
  text-underline-offset: 7px;
}

.navbar-dark .navbar-nav .nav-link.active {
  color: #a1ff7f;
  text-decoration: underline;
  text-decoration-thickness: auto;
  text-decoration-thickness: 2px;
  text-underline-offset: 7px;
}

.avatar-nav {
  border-radius: 50%;
  height: 43px;
  margin-left: 10px;
}

.dropdown-menu-dark .dropdown-item.active,
.dropdown-menu-dark .dropdown-item:focus {
  background-color: #262b30;
}

.offcanvas {
  background-color: #1c2024;
}

.offcanvas-header .btn-close {
  background: transparent
    url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='%23ddd'%3e%3cpath d='M.293.293a1 1 0 011.414 0L8 6.586 14.293.293a1 1 0 111.414 1.414L9.414 8l6.293 6.293a1 1 0 01-1.414 1.414L8 9.414l-6.293 6.293a1 1 0 01-1.414-1.414L6.586 8 .293 1.707a1 1 0 010-1.414z'/%3e%3c/svg%3e")
    center/1em auto no-repeat;
}
</style>

<script>
import news from "@/assets/news.json";

export default {
  Name: "TheNavbar",
  data() {
    return {
      news: news,
    };
  },
  methods: {
    collapseNav() {
      let navCollapse = document.getElementById("navbarSupportedContent");
      navCollapse.classList.remove("show");
    },
    changeActiveCharacter(i) {
      this.$store.commit("changeActiveCharacter", i);
      this.collapseNav();
    },
    seenLatestNews() {
      this.$store.commit("seenLatestNews", news.latestID);
    },
  },
};
</script>
