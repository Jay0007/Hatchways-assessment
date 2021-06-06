<template>
  <v-container>
    <v-card class="ma-5">
      <v-text-field
        label="Search by name"
        single-line
        v-model="nameSearch"
        class="mx-2 pb-0"
        hide-details
      ></v-text-field>
      <v-text-field
        label="Search by tag"
        single-line
        v-model="tagSearch"
        class="mx-2"
      ></v-text-field>
      <v-expansion-panels accordion flat multiple>
        <v-expansion-panel
          v-for="(item, k) in filteredList"
          :key="k"
          class="pt-0"
        >
          <v-list class="pa-0">
            <v-expansion-panel-header
              v-slot="{ open }"
              hide-actions
              class="pl-0"
              style="align-items:flex-start"
            >
              <v-list-item
                :key="item.firstName"
                class="pt-0"
                style="align-items:flex-start"
              >
                <v-list-item-avatar
                  size="100"
                  style="border: 1px solid lightgrey;"
                >
                  <v-img :src="item.pic"></v-img>
                  <!-- <v-icon :class="outlined" v-text="item.pic"></v-icon> -->
                </v-list-item-avatar>

                <v-list-item-content style="align-items:flex-start">
                  <v-list-item-title class="text-h3 text-uppercase">
                    {{ item.firstName }}
                  </v-list-item-title>
                  <div class="pl-4">
                    <v-list-item-subtitle class="pb-1">
                      Email: {{ item.email }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle class="pb-1">
                      Company: {{ item.company }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle class="pb-1">
                      Skill: {{ item.email }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle class="pb-1">
                      Average: {{ average(item.grades) }} %
                    </v-list-item-subtitle>
                  </div>
                </v-list-item-content>
              </v-list-item>
              <v-fade-transition class="align-start">
                <v-icon
                  color="primary"
                  v-if="open"
                  style="justify-content: flex-end;"
                  >mdi-minus</v-icon
                >
                <v-icon
                  color="primary"
                  v-else
                  style="justify-content: flex-end;"
                  >mdi-plus</v-icon
                >
              </v-fade-transition>
            </v-expansion-panel-header>
          </v-list>
          <v-expansion-panel-content>
            <v-row>
              <v-col cols="2" offset="1">
                <div
                  v-for="(j, index) in item.grades"
                  :key="index"
                  class="d-flex flex-start ml-9 text-lighten-6"
                >
                  Test {{ index + 1 }}: <span class="ml-5"> {{ j }}%</span>
                </div>
              </v-col>
            </v-row>
          </v-expansion-panel-content>
          <v-row class="ml-10">
            <v-col offset="1">
              <v-chip
                label
                v-for="(j, index) in item.taglist"
                v-model="item.taglist[`${index}`]"
                :key="index"
                class="mx-1"
              >
                {{ j }}
              </v-chip>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="2" offset="1">
              <v-text-field
                dense
                class="d-flex flex-start ml-14"
                label="Add a tag"
                single-line
                v-model="item.tag"
                @keydown.enter="addTag(item)"
              ></v-text-field>
            </v-col>
          </v-row>

          <v-divider></v-divider>
        </v-expansion-panel>
      </v-expansion-panels>
    </v-card>
  </v-container>
</template>

<script>
export default {
  name: "HelloWorld",

  data: () => ({
    list: [],
    list4: [],
    tag: "",
    nameSearch: "",
    tagSearch: "",
  }),
  watch: {},
  created() {
    this.$axios
      .get("https://api.hatchways.io/assessment/students")
      .then((response) => {
        this.list = response.data.students;
        this.list.forEach((item) => {
          this.$set(item, "tag", null);
          this.$set(item, "taglist", []);
        });
      })
      .catch((er) => {
        console.log(er);
      });
  },
  methods: {
    addTag(item) {
      item.taglist.push(item.tag.toLowerCase());
      this.$set(item, "tag", null);
    },
    nameFilter(list) {
      var that = this;
      return list.filter(
        (list) =>
          list.firstName.toLowerCase().indexOf(that.nameSearch.toLowerCase()) >=
          0
      );
    },
    tagFilter(list) {
      let list4 = list;
      if (this.tagSearch) {
        list4 = [];
        for (let index = 0; index < list.length; index++) {
          for (let index2 = 0; index2 < list[index].taglist.length; index2++) {
            if (
              list[index].taglist[index2].indexOf(
                this.tagSearch.toLowerCase()
              ) >= 0
            ) {
              list4.push(list[index]);
              break;
            }
          }
        }
        return list4;
      } else {
        return list4;
      }
      // let that = this;
      // return list.filter((list2) => {
      //   if (list2.taglist) {
      //     list2.taglist.filter((list3) => {
      //       if (list3.indexOf(that.tagSearch) >= 0) {
      //         return true;
      //       } else {
      //         return false;
      //       }
      //     });
      //   } else {
      //     return false;
      //   }
      // });
    },
    average(grades) {
      let sum = 0;
      for (let i = 0; i < grades.length; i++) {
        sum += parseInt(grades[i], 10); //don't forget to add the base
      }

      return sum / grades.length || undefined;
    },
  },
  computed: {
    filteredList() {
      return this.tagFilter(this.nameFilter(this.list));
    },
  },
};
</script>
