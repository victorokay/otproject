<template>
  <div @click="getdrawingfile">
    <h2>图档模块</h2>
    <!-- <el-button @click="getdata">获取按钮</el-button> -->
    <el-row>
      <el-col
        v-for="list in showLists"
        :key="list['product_name']"
        :span="8"
        :offset="list['index'] > 0 ? 2 : 0"
      >
        <el-card :body-style="{padding: '0px'}">
          <span>
            <img style="width: 50px; height: 50px" src="" />
          </span>
          <div style="padding: 14px">
            <span>{{ list["product_name"] }}</span
            ><br />
            <span>{{ list["devices_name"] }}</span
            ><br />
            <span>{{ list["tenant_id"] }}</span
            ><br />
            <span>{{ list["name"] }}</span
            ><br />
            <span>{{ list["file_name"] }}</span
            ><br />
            <span>{{ list["file_path"] }}</span
            ><br />
            <span>{{ list["resource_type"] }}</span
            ><br />
            <span>{{ list["resource_type_str"] }}</span
            ><br />
            <div class="bottom">
              <time class="time">时间：</time>
              <!-- <div>
                            <pdf>
                            </pdf>
                        </div> -->
              <el-button type="primary" plain>预览</el-button>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      showLists: [], //存储渲染数据
      product_name: "", //产品名称
      devices_name: "", //机台名称
      tenant_id: "",
      name: "",
      drawingfile_name: "",
      drawingfile_file_name: "", //文件名
      drawingfile_file_path: "", //文件地址
      resource_type: "", //类型英文
      resource_type_str: "", //类型中文
    };
  },

  // beforeCreate() {
  //     this.showList.push('12345')
  //     console.log(this.showList)
  // },
  // 数据初始化，created是发生在mounted之前的，template还没有被渲染成html，但是在这个阶段已经可以获取到props和data了，所以在这一个步进行数据赋值是比较恰当的.mounted 顾名思义是页面已经渲染完成了，template已经被渲染成了html，这个时候再进行数据请求，很有可能会造成页面闪屏。

  beforeMount() {
    // 通过API接口获取 资料数据 信息
    // 获取文档档图纸
    // axios
    //   .post(
    //     "/api/public/cloud-api/file-management/fileDownload", //API调取准备
    //     {
    //       //图档地址
    //       filePath: "/_system/N863613   R3/正式工艺/机加工/N863613检测.jpg",
    //     }
    //   )
    //   .then((res) => {
    //     console.log(res);
    //   })
    //   .catch((err) => {
    //     alert(err);
    //   });
  },
  methods: {
    getdrawingfile() {
      axios
        .get(
          "/api/public/productSchedule/standard/?page_index=1&show_count=100",
          {
            headers: {
              Authorization: "Bearer " + localStorage.getItem("Authorization"),
              // "Content-Type": "application/json;charset=UTF-8",
              page_index: "1",
              show_count: "20",
            },
            //将token存入请求头
          }
        )
        .then((res) => {
          //TODOheaders请求头的乱码数据转换为中文,
          //TODO获取图档不成功
          // const fileName = decodeURI(res.headers['content-disposition'].split(';')[1].split('=')[1])

          //启用两个forEach循环，进行循环获取数据
          //第一，通过循环获取data()下所有机台下属信息
          //第二，通过循环获取客制的模块下属数据信息

          //TODO选中机台，可获取图档，但需要刷新后才能获得

          const data_get = res.data.content.data; //得到数组data[]数据
          data_get.forEach((item) => {
            const getDevice = localStorage.getItem("getDevice");
            const devices_name = item.devices_name;
            if (getDevice === devices_name) {
              const product_name = item.product_name;
              const tenant_id = item.tenant_id;
              //data[]数组下的每个数据进行循环，再次深入循环获取
              item?.custom_data_resource_columns?.drawingfile.forEach(
                (item1) => {
                  const name = item1.name;
                  const file_name = item1.file_name;
                  const file_path = item1.file_path;

                  const resource_type = item1.columnAttribute.resource_type;
                  const resource_type_str =
                    item1.columnAttribute.resource_type_str;

                  // 将数据存入数组内对象
                  this.showLists.push({
                    devices_name: devices_name,
                    product_name: product_name,
                    tenant_id: tenant_id,
                    name: name,
                    file_name: file_name,
                    file_path: file_path,
                    resource_type: resource_type,
                    resource_type_str: resource_type_str,
                  });
                }
              );
            }
          });
        })
        .catch((err) => {
          alert(err);
        });
    },
  },
};
</script>
<style>
.time {
  font-size: 12px;
  color: #999;
}

.bottom {
  margin-top: 13px;
  line-height: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.button {
  padding: 0;
  min-height: auto;
}

.image {
  width: 100%;
  display: block;
}
</style>
