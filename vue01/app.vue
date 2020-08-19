<template>
<a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
        <a-form layout="inline" @keyup.enter.native="searchQuery">
            <a-row :gutter="24">
                <a-col :md="6" :sm="8">
                    <a-form-item label="专区名称">
                        <j-input placeholder="请输入活专区名称" v-model="queryParam.name"></j-input>
                    </a-form-item>
                </a-col>
                <a-col :md="6" :sm="8">
                    <a-form-item label="状态">
                        <a-select v-model="queryParam.status">
                            <a-select-option v-for="item in dictOptions.status" :value="item.value">{{item.text}}</a-select-option>
                        </a-select>
                    </a-form-item>
                </a-col>
                <a-col :md="6" :sm="8">
                    <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                        <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
                        <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
                        <!-- <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a> -->
                    </span>
                </a-col>

            </a-row>
        </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
        <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>

        <!--<a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>-->
    </div>

    <!-- table区域-begin -->
    <div>
        <!--<div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>-->

        <a-table ref="table" size="middle" bordered rowKey="id" :columns="columns" :dataSource="dataSource" :pagination="ipagination" :loading="loading" :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}" @change="handleTableChange">

            <template slot="htmlSlot" slot-scope="text">
                <div v-html="text"></div>
            </template>
            <template slot="imgSlot" slot-scope="text">
                <span v-if="!text" style="font-size: 12px;font-style: italic;">无此图片</span>
                <img v-else :src="getImgView(text)" height="25px" alt="图片不存在" style="max-width:80px;font-size: 12px;font-style: italic;" />
            </template>
            <template slot="fileSlot" slot-scope="text">
                <span v-if="!text" style="font-size: 12px;font-style: italic;">无此文件</span>
                <a-button v-else :ghost="true" type="primary" icon="download" size="small" @click="uploadFile(text)">
                    下载
                </a-button>
            </template>

            <span slot="action" slot-scope="text, record">
                <a @click="handleEdit(record)">编辑</a>
                <a-divider type="vertical" />
                <a @click="promotionProductManage(record)">参与商品</a>
                <a-divider type="vertical" />
                <a-dropdown>
                    <a class="ant-dropdown-link">更多
                        <a-icon type="down" /></a>
                    <a-menu slot="overlay">
                        <a-menu-item>
                            <!--<a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>-->
                            <a-popconfirm title="确定需要复制吗?" @confirm="() => handleCopy(record.id)">
                                <a>一键复制</a>
                            </a-popconfirm>
                        </a-menu-item>
                    </a-menu>
                </a-dropdown>
            </span>

        </a-table>
    </div>

    <promotion-modal ref="modalForm" v-bind:promotionType="promotionType" @ok="modalFormOk"></promotion-modal>

    <promotion-full-reduce-list ref="fullReduceProductList" v-bind:promotionId="selectPromotionId"></promotion-full-reduce-list>
    <promotion-group-list ref="groupProductList" v-bind:promotionId="selectPromotionId"></promotion-group-list>
    <promotion-flash-list ref="flashProductList" v-bind:promotionId="selectPromotionId"></promotion-flash-list>

    <promotion-vertical-fall-list ref="verticalFallProductList" v-bind:promotionId="selectPromotionId"></promotion-vertical-fall-list>
</a-card>
</template>

<script>
import {
    JeecgListMixin
} from '@/mixins/JeecgListMixin'
import PromotionModal from './modules/PromotionTodayModal'
import PromotionFullReduceList from './PromotionFullReduceList'
import PromotionGroupList from './PromotionGroupList'
import PromotionFlashList from './PromotionFlashList'
import PromotionVerticalFallList from './PromotionVerticalFallList'
import {
    initDictOptions,
    filterMultiDictText
} from '@/components/dict/JDictSelectUtil'
import AInput from "ant-design-vue/es/input/Input";
import {
    formatDate
} from "@/utils/util"
import JInput from '@/components/jeecg/JInput'
import {
    postAction
} from '@/api/manage'

export default {
    name: "PromotionTodayList",
    mixins: [JeecgListMixin],
    components: {
        AInput,
        JInput,
        PromotionModal,
        PromotionFullReduceList,
        PromotionGroupList,
        PromotionFlashList,
        PromotionVerticalFallList,
    },
    data() {
        return {
            description: '活动主信息管理页面',
            queryParam: {
                type: this.promotionType
            },
            // 表头
            columns: [{
                    title: '专区名称',
                    align: "center",
                    dataIndex: 'name'
                },
                {
                    title: '开始时间',
                    align: "center",
                    dataIndex: 'beginTime',
                    customRender: function (text) {
                        return !text ? "" : (text.length > 19 ? text.substr(0, 19) : text)
                    }
                },
                {
                    title: '结束时间',
                    align: "center",
                    dataIndex: 'endTime',
                    customRender: function (text) {
                        return !text ? "" : (text.length > 19 ? text.substr(0, 19) : text)
                    }
                },
                {
                    title: '进行状态',
                    align: "center",
                    dataIndex: 'proceedStatus',
                    customRender: (text, record) => {
                        let now = formatDate(new Date().getTime(), "yyyy-MM-dd hh:mm:ss");
                        if (now < record.beginTime) {
                            return '未开始';
                        } else if (record.beginTime <= now && now <= record.endTime) {
                            return '进行中';
                        } else if (record.endTime < now) {
                            return '已结束';
                        }
                    }
                },
                {
                    title: '开启状态',
                    align: "center",
                    dataIndex: 'status',
                    customRender: (text) => {
                        return filterMultiDictText(this.dictOptions['status'], text + "")
                    }
                },
                {
                    title: '创建时间',
                    align: "center",
                    dataIndex: 'createTime',
                    customRender: function (text) {
                        return !text ? "" : (text.length > 19 ? text.substr(0, 19) : text)
                    }
                },
                {
                    title: '操作',
                    dataIndex: 'action',
                    align: "center",
                    scopedSlots: {
                        customRender: 'action'
                    }
                }
            ],
            url: {
                list: "/product/promotion/list",
                delete: "/product/promotion/delete",
                deleteBatch: "/product/promotion/deleteBatch",
                exportXlsUrl: "/product/promotion/exportXls",
                importExcelUrl: "product/promotion/importExcel",
            },
            dictOptions: {
                status: [{
                    "value": "0",
                    "text": "未启用"
                }, {
                    "value": "1",
                    "text": "已启用"
                }],
                type: [{
                    "value": "1",
                    "text": "拼团"
                }, {
                    "value": "2",
                    "text": "限时抢购"
                }, {
                    "value": "3",
                    "text": "满减"
                }, {
                    "value": "6",
                    "text": "直降"
                }],
            },

            selectPromotionId: null
        }
    },
    props: {
        promotionType: {
            type: Number,
            required: true
        },
    },
    computed: {
        importExcelUrl: function () {
            return `${this.VUE_APP_GLOBAL_DOMAIN}/${this.url.importExcelUrl}`;
        }
    },
    mounted() {
        console.log('----promotionType---', this.promotionType)
    },
    methods: {
        initDictConfig() {},
        searchReset() {
            this.queryParam = {
                type: this.promotionType
            }
            this.loadData(1);
        },
        promotionProductManage(record) {
            this.selectPromotionId = record.id;
            if (record.type == 1) {
                this.$refs.groupProductList.open(record);
                this.$refs.groupProductList.title = record.name;
            } else if (record.type == 2) {
                this.$refs.flashProductList.open(record);
                this.$refs.flashProductList.title = record.name;
            } else if (record.type == 3) {
                // 满减
                this.$refs.fullReduceProductList.open(record);
                this.$refs.fullReduceProductList.title = record.name;
            } else if (record.type == 6) {
                // 直降
                this.$refs.verticalFallProductList.open(record);
                this.$refs.verticalFallProductList.title = record.name;
            }
        },
        handleCopy(id) {
            postAction('/product/promotionOnKey/onKeyCopy', {
                id: id
            }).then((res) => {
                if (res.success) {
                    this.loadData(1);
                    this.$message.success('复制成功');
                } else {
                    this.$message.warning(res.message);
                }
            })
        }
    }
}
</script>

<style scoped>
@import '~@assets/less/common.less'
</style>
