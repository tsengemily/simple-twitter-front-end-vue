<template>
  <!-- Modal -->
  <div
    class="modal fade"
    id="user-edit"
    tabindex="-1"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
  >
    <form @submit.stop.prevent="handleSubmit($event)">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <div class="d-flex">
              <button
                type="button"
                class="close-btn"
                data-dismiss="modal"
                aria-label="Close"
              >
                <span aria-hidden="true">
                  <img src="../assets/close.png" class="close-icon" />
                </span>
              </button>
              <div class="edit-title">編輯個人資料</div>
            </div>
          </div>
          <button type="submit" class="edit-close">儲存</button>
          <div class="modal-body">
            <div class="edit-cover">
              <img :src="user.cover | emptyCover" class="cover-img" />
              <label for="cover-input" class="cover-label"></label>
              <input
                id="cover-input"
                type="file"
                accept="image/*"
                name="cover"
                @change="handleCoverFileChange"
                class="cover-input"
              />
            </div>
            <div class="edit-avatar">
              <img :src="user.avatar | emptyImage" class="avatar-img" />
              <label for="avatar-input" class="avatar-label"> </label>
              <input
                id="avatar-input"
                type="file"
                accept="image/*"
                name="avatar"
                @change="handleAvatarFileChange"
                class="avatar-input"
              />
            </div>
            <div class="edit-name">
              <label for="">名稱</label>
              <input
                type="text"
                class="name-input"
                value="user.name"
                name="name"
                v-model="user.name"
                required
              />
              <div class="word-count">{{ nameCount }}/50</div>
            </div>
            <div class="edit-introduction">
              <textarea
                name="introduction"
                id=""
                rows="10"
                class="introduction-input"
                placeholder="自我介紹"
                v-model="user.introduction"
              >
              </textarea>
              <div class="word-count">{{ introductionCount }}/160</div>
            </div>
          </div>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import UserAPI from "../apis/user";
import { Toast } from "../utils/helpers";
import { emptyImageFilter } from "../utils/mixins";
import { emptyCoverFilter } from "../utils/mixins";

export default {
  props: {
    initialUser: {
      type: Object,
      default: () => ({
        id: -1,
        name: "",
        email: "",
        account: "",
        cover: "",
        avatar: "",
        introduction: "",
      }),
    },
  },
  data() {
    return {
      user: {
        ...this.initialUser,
      },
      avatarIcon: [],
    };
  },
  computed: {
    nameCount() {
      return this.user.name.length;
    },
    introductionCount() {
      if (this.user.introduction) {
        return this.user.introduction.length;
      } else {
        return 0;
      }
    },
  },
  methods: {
    handleCoverFileChange(event) {
      const { files } = event.target;
      if (files.length === 0) return;
      const imgURL = window.URL.createObjectURL(files[0]);
      this.user.cover = imgURL;
    },
    handleAvatarFileChange(event) {
      const { files } = event.target;
      if (files.length === 0) return;
      const imgURL = window.URL.createObjectURL(files[0]);
      this.user.avatar = imgURL;

      // fileReader
      // const arrayBuffer = getArrayBuffer(event.target.files[0]);
      // console.log("event.target.files[0]", event.target.files[0]);
      // this.avatarIcon = Array.from(new Uint8Array(arrayBuffer));

      // function getArrayBuffer(file) {
      //   return new Promise((resolve, reject) => {
      //     // STEP 3: 轉成 ArrayBuffer, i.e., reader.result
      //     const reader = new FileReader();
      //     console.log("reject", reject);
      //     reader.addEventListener("load", () => {
      //       resolve(reader.result);
      //     });
      //     reader.readAsArrayBuffer(file);
      //   });
      // }
    },
    async handleSubmit(e) {
      try {
        if (!this.user.name) {
          Toast.fire({
            icon: "warning",
            title: "名稱為必填",
          });
          return;
        } else if (this.nameCount > 50) {
          Toast.fire({
            icon: "warning",
            title: "名稱不可超過50字",
          });
          return;
        } else if (this.introductionCount > 160) {
          Toast.fire({
            icon: "warning",
            title: "自我介紹不可超過160字",
          });
          return;
        }
        const form = e.target; // <form></form>
        const formData = new FormData(form);
        for (let [name, value] of formData.entries()) {
          console.log(name + ": " + value);
        }
        const response = await UserAPI.editProfile({
          userId: this.user.id,
          formData,
        });
        console.log(response);

        const { data } = await UserAPI.get({ userId: this.user.id });
        console.log("datadatadatadatadatadata", data);
        localStorage.setItem("avatar", data.avatar);

        location.reload();
      } catch (error) {
        console.log("error", error);
        Toast.fire({
          icon: "error",
          title: "無法更新資料，請稍後再試",
        });
      }
    },
  },
  mixins: [emptyImageFilter, emptyCoverFilter],
};
</script>


<style scoped>
.modal-header {
  position: relative;
}

.close-btn {
  border: none;
  background-color: transparent;
  width: 30px;
  height: 30px;
}

.close-icon {
  width: 15px;
  height: 15px;
}

.modal-body {
  position: relative;
  top: 50px;
}

.edit-title {
  position: absolute;
  top: 23px;
  left: 80px;
  font-size: 19px;
  font-weight: 700;
  color: #1c1c1c;
}

.edit-close {
  position: absolute;
  top: 15px;
  right: 20px;
  border-radius: 100px;
  border: none;
  padding: 6px 14px;
  color: #fff;
  background-color: #ff6600;
}

.edit-cover {
  position: relative;
  /* outline: 1px solid red; */
}

.cover-img {
  width: 100%;
  height: 200px;
  margin-bottom: 40px;
}

.cover-label,
.avatar-label {
  /* outline: 1px solid red; */
  width: 35px;
  height: 35px;
  background-image: url("../assets/update.png");
  background-size: 70%;
  background-repeat: no-repeat;
  background-position: center center;
  background-color: #657786;
  border-radius: 50%;
  cursor: pointer;
}

.cover-label {
  position: absolute;
  top: 80px;
  left: 45%;
}

.avatar-label {
  position: relative;
  z-index: 10;
  top: -70px;
  left: 60px;
}

.cover-input,
.avatar-input {
  display: none;
}

.avatar-img {
  position: absolute;
  top: 140px;
  left: 30px;
  z-index: 10;
  border-radius: 50%;
  border: 4px solid #fff;
  width: 120px;
  height: 120px;
}

.edit-name,
.edit-introduction {
  position: relative;
  font-weight: 500;
  font-size: 15px;
  color: #657786;
  margin-bottom: 40px;
}

.edit-name > label {
  position: absolute;
}

.name-input,
.introduction-input {
  width: 100%;
  border: none;
  border-bottom: 2px solid #657786;
  background-color: #f5f8fa;
}

.name-input {
  height: 54px;
  padding: 10px 0 0 15px;
  font-size: 19px;
  line-height: 28px;
  font-weight: 500;
}

.introduction-input {
  height: 150px;
  padding: 10px;
}

.word-count {
  text-align: end;
}
</style>
