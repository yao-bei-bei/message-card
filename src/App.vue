<template>
  <div class="dragCom">

<!--    <button @click="queryObj.num++">改变查询条件</button>-->
    <div class="body">
      <div class="left">
        <draggable class="left" :list="leftComList" :group="'people'">
          <div
            ref="comBody"
            v-for="(config, index) in leftComList"
            :key="index"
            class="comCard"
          >
            <ComponentRender :config="config" @handleDelete="handleDeleteCom" />
          </div>
        </draggable>
      </div>
      <div class="right">
        <draggable
          class="dragArea"
          :list="rightComList"
          :group="{ name: 'people', pull: 'clone', put: false }"
          :clone="handleCloneDog"
        >
          <div class="card" v-for="element in rightComList" :key="element.id">
            {{ element.name }}
          </div>

        </draggable>
        <json-viewer :value="leftComList" :expand-depth=1  sort></json-viewer>
      </div>
    </div>

  </div>
</template>

<script>
  import draggable from "vuedraggable";

  // 想要动态生成的组件，先引入这个文件。
  import components1 from "./components/TestCom1.vue";
  import components2 from "./components/TestCom2.vue";
  import components3 from "./components/TestCom2.vue";
  // 将组件的名称和组件做一个对应Map
  const comMap = {
    components1,
    components2,
  };
  export default {
    components: {
      draggable,
      ...comMap,
      ComponentRender: {
        functional: true,
        props: {
          config: Object,
        },
        render(h, context) {
          const { config } = context.props;
          return h(config.name, {
            ...context.data,
            props: config.props,
          });
        },
      },
    },
    data() {
      return {
        rightComList: [
          {
            name: "components1",
          },
          {
            name: "components2",
          },
          {
            name: "components3",
          },
        ],
        leftComList: [], // 存储驱动动态生成组件的数据。

        queryObj: {
          // 主要的作用就是向子组件传递查询条件
          num: 0,
        },
      };
    },
    methods: {
      getComponent(name) {
        switch (name) {
          case "components1":
            return {
              props: {
                // queryObj: this.queryObj,
                comItem: {
                  name: name,
                  id: 1,
                  msg: "模板1",
                },
              },
            };
          case "components2":
            return {
              props: {
                // queryObj: this.queryObj,
                comItem: {
                  name: name,
                  id: 2,
                  msg: "模板2",
                },
              },
            };
          case "components3":
            return {
              props: {
                // queryObj: this.queryObj,
                comItem: {
                  name: name,
                  id: 3,
                  msg: "模板3",
                },
              },
            };
        }
      },
      handleDeleteCom({ id }) {
        // 传递给子组件删除的方法，根据组件的id来删除数据
        const index = this.leftComList.findIndex(
          (item) => item.props.comItem.id === id
        );
        if (~index) {
          // 如果存在这个id的组件，就删除
          this.leftComList.splice(index, 1);
        }
      },
      handleCloneDog(item) {
        console.log(item)
        return {
          name: item.name,
          ...this.getComponent(item.name),
        };
      },
    },
  };
</script>

<style>
  .dragCom {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  .body {
    width: 100%;
    height: 800px;
    display: flex;
    justify-content: space-between;
  }

  .left {
    flex: 1;
    height: 800px;
    border: 1px solid pink;
  }

  .right {
    width: 20%;
    height: 800px;
  }

  .card {
    height: 50px;
    background-color: #40cec7;
    margin: 12px 0;
    font-size: 12px;
    line-height: 50px;
    cursor: pointer;
  }

  .comCard {
    margin: 12px;
    display: inline-block;
  }
</style>
