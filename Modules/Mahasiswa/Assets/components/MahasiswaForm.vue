<template>
  <div>
    <div class="row page-titles">
      <div class="col-md-5 align-self-center">
        <h3 class="text-themecolor">
          {{ trans("mahasiswas.create resource") }}
        </h3>
      </div>
      <div class="col-md-7 align-self-center">
        <ol class="breadcrumb">
          <li class="breadcrumb-item">
            <a href="/">
              {{ trans("core.breadcrumb.home") }}
            </a>
          </li>
          <li class="breadcrumb-item">
            {{ trans("mahasiswas.create resource") }}
          </li>
        </ol>
      </div>
    </div>
    <div class="container">
      <div class="card">
        <div class="card-body">
          <el-form
            :model="mahasiswa"
            status-icon
            :rules="rules"
            ref="mahasiswa"
            label-width="120px"
            label-position="top"
            v-loading="loading"
          >
            <el-form-item :label="trans('mahasiswas.form.name')" prop="nama">
              <el-input v-model="mahasiswa.nama" autocomplete="off"></el-input>
              <div
                class="el-form-item__error"
                v-if="form.errors.has('nama')"
                v-text="form.errors.first('nama')"
              ></div>
            </el-form-item>
            <el-form-item :label="trans('mahasiswas.form.nim')" prop="nim">
              <el-input
                type="number"
                v-model="mahasiswa.nim"
                autocomplete="off"
              ></el-input>
              <div
                class="el-form-item__error"
                v-if="form.errors.has('nim')"
                v-text="form.errors.first('nim')"
              ></div>
            </el-form-item>
            <el-form-item :label="trans('mahasiswas.form.email')" prop="email">
              <el-input
                type="email"
                v-model="mahasiswa.email"
                autocomplete="off"
              ></el-input>
              <div
                class="el-form-item__error"
                v-if="form.errors.has('email')"
                v-text="form.errors.first('email')"
              ></div>
            </el-form-item>
            <el-form-item
              :label="trans('mahasiswas.form.jurusan')"
              prop="jurusan"
            >
              <el-select v-model="mahasiswa.jurusan" placeholder="Jurusan">
                <el-option
                  v-for="(option,i) in computed_option_jurusan"
                  :key="i"
                  :label="option.value"
                  :value="option.value"
                ></el-option>
              </el-select>
              <div
                class="el-form-item__error"
                v-if="form.errors.has('jurusan')"
                v-text="form.errors.first('jurusan')"
              ></div>
            </el-form-item>
          </el-form>
          <InfoJurusan :jurusan="computed_jurusan"/>
        </div>
        <div class="card-footer">
          <el-button type="primary" @click="submitForm('mahasiswa')">
            {{ button_submit_text }}
          </el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import InfoJurusan from "./InfoJurusan.vue";
import Form from "form-backend-validation";
export default {
  data() {
    return {
      loading: false,
      option_jurusan: [
        {
          type: '1',
          value: 'Teknik Informatika'
        }, {
          type: '1',
          value: 'Teknik Mesin'
        },{
          type:'0',
          value:"Sistem Informasi",
        }, {
          type: '0',
          value: "Elektronika",
        }        ,
      ],
      form: new Form(),
      mahasiswa: {
        nama: null,
        nim: null,
        email: null,
        id: null,
        jurusan: null,
      },
      rules: {
        nama: [
          {
            required: true,
            message: "Data diperlukan",
            trigger: "change",
          },
        ],
        nim: [
          {
            required: true,
            message: "Data diperlukan",
            trigger: "change",
          },
        ],
        email: [
          {
            required: true,
            message: "Data diperlukan",
            trigger: "change",
          },
        ],
        id: [
          {
            required: true,
            message: "Data diperlukan",
            trigger: "change",
          },
        ],
        jurusan: [
          {
            required: true,
            message: "Data diperlukan",
            trigger: "change",
          },
        ],
      },
    };
  },
  components: {
    InfoJurusan
  },

  methods: {
    getRoutes() {
      if (this.$route.params.mahasiswa) {
        return route("api.mahasiswa.mahasiswa.update", {
          mahasiswa: this.$route.params.mahasiswa,
        });
      }

      return route("mahasiswa.mahasiswas.create");
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.$confirm(
            this.trans("mahasiswas.messages.create mahasiswa"),
            this.trans("mahasiswas.title.create mahasiswa")
          )
            .then(() => {
              this.loading = true;
              this.form = new Form(this.mahasiswa);
              this.form
                .post(this.getRoutes(), this.mahasiswa)
                .then((response) => {
                  this.loading = false;
                  this.$message({
                    type: "success",
                    message: response.message,
                  });

                  this.$router.push({
                    name: "admin.mahasiswa.mahasiswa.index",
                  });
                })
                .catch((error) => {
                  console.log(error);
                  this.loading = false;
                  this.$notify.error({
                    title: "Error",
                    message: "There are some errors in the form.",
                  });
                });
            })
            .catch(() => {
              this.$message({
                type: "info",
                message: "Cancelled",
              });
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    fetchData() {
      axios
        .get(
          route("api.mahasiswa.mahasiswa.find", {
            mahasiswa: this.$route.params.mahasiswa,
          })
        )
        .then((response) => {
          console.log(response.data);
          this.mahasiswa = response.data.data;
        });
    },
  },
  mounted() {
    // console.log();
    if (this.$route.params.mahasiswa) {
      this.fetchData();
    }
  },
  watch: {
    'mahasiswa.jurusan': function() {
      alert("Jurusan berubah")
    }
  },
  computed: {
    computed_jurusan() {
      var jurusan = this.option_jurusan.find(jurusan => jurusan.value == this.mahasiswa.jurusan);

      if(jurusan) {
        return jurusan.value;
      }

      return "Jurusan Belum Dipilih"
    },
    computed_option_jurusan() {
      return this.option_jurusan.filter(jurusan => jurusan.type =='1')
    },
    button_submit_text() {
      if (this.$route.params.mahasiswa) {
        return this.trans("mahasiswas.button.update mahasiswa");
      }
      return this.trans("mahasiswas.button.create mahasiswa");
    },
  },
};
</script>

<style></style>
