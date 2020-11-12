<!-- 模块:<%= moduleName %> 功能:<%= className %> 作者: <%= authorName %> 日期: <%= createTime %> -->
<template>
    <audaque-table-list-page>
        <!-- 标题栏 -->
        <template slot="title">
            <audaque-table-title :title="$route.meta.name"><% for(var i = 0; i < optionButtons.length; i++){ %> <% if  (optionButtons[i]==="新增")  {%>
                <audaque-action-icon
                    v-audaque-permission="{ key: audaqueIconTypeMixin.create, list: audaquePermissionMixin }"
                    iconClass="main-add"
                    title="新增"
                    @click="audaqueAddItemMixin"
                ></audaque-action-icon><% } %> <% } %>
                <audaque-action-icon
                    iconClass="main-list-search"
                    title="筛选"
                    @click="audaqueToggleSearchMinix"
                ></audaque-action-icon>
            </audaque-table-title>
        </template>
        <!-- 搜索栏 -->
        <template slot="search">
            <audaque-table-search
                @close="audaqueSearchBarVisibleMixin = false"
                v-show="audaqueSearchBarVisibleMixin"
                @search="audaqueSearchListMixin"
                @reset="audaqueResetListMixin"
            ><% for(var i = 0; i < searchItems.length; i++){ %> <% if (searchItems[i].options) { %>
                <audaque-table-search-item title="<%= searchItems[i].colCNName %>">
                    <el-select 
                        v-model="audaqueSearchMixin.<%= searchItems[i].colName %>" 
                        placeholder="<%= searchItems[i].placeholder %>"
                    >
                        <el-option
                            v-for="item in <%= searchItems[i].options %>"
                            :key="item.name"
                            :label="item.name"
                            :value="item.value"
                        >
                        </el-option>
                    </el-select>
                </audaque-table-search-item> <% } else if (searchItems[i].jsType==="date") { %>
                <audaque-table-search-item title="<%= searchItems[i].colCNName %>" >
                    <el-date-picker 
                        clearable 
                        v-model="audaqueSearchMixin.<%= searchItems[i].colName %>" 
                        type="daterange" 
                        range-separator="-"
                        start-placeholder="开始日期" 
                        end-placeholder="结束日期"
                    >
                    </el-date-picker>
                </audaque-table-search-item> <% }else{ %>
                <audaque-table-search-item title="<%= searchItems[i].colCNName %>">
                    <el-input
                        v-model.trim="audaqueSearchMixin.<%= searchItems[i].colName %>"
                        placeholder="<%= searchItems[i].placeholder %>"
                        clearable
                        :maxlength="<%= searchItems[i].colLength %>"
                    ></el-input>
                </audaque-table-search-item> <% } %> <% } %>
            </audaque-table-search>
        </template>
        <!-- 列表 -->
        <template>
            <audaque-table
                :data="audaqueDataListMixin.datas"
                :table-fetch-status="audaqueTableFetchStatusMixin"
                @request-list="audaqueRequestListMixin" <% if(selection) { %> 
                @selection-change="handleSelectionChange" <% } %>
            ><% if(selection) {%>
                <el-table-column type="selection"></el-table-column> <% } %>
                <el-table-column
                    :width="audaqueIndexWidthMixin"
                    label="序号"
                    type="index"
                    indexMethod="audaqueIndexMethodMixin"
                ></el-table-column> <% for(var i = 0; i < tableColumns.length; i++){ %> <% if (viewColumnName=== tableColumns[i].colName) { %>
                <!--点击此列查看详情-->
                <el-table-column 
                    prop="<%= tableColumns[i].colName %>" 
                    label="<%= tableColumns[i].colCNName %>" 
                    show-overflow-tooltip
                >
                    <template slot-scope="scope">
                        <audaque-link-text
                            :permission="{ key: audaqueIconTypeMixin.read, list: audaquePermissionMixin }"
                            @click="audaqueGotoEditPageMixin(scope.row, audaqueFormTypeMixin.view)"
                            type="key"
                            :title="scope.row.<%=  tableColumns[i].colName %>"
                        ></audaque-link-text>
                    </template>
                </el-table-column> <% } else if (tableColumns[i].options) { %>
                <!--枚举值类型-->
                <el-table-column
                    label="<%= tableColumns[i].colCNName %>"
                    show-overflow-tooltip
                >
                    <template #default="scope">
                        <span>{{getEnumLabel(<%= tableColumns[i].options %>,scope.row.<%= tableColumns[i].colName %>)}}</span>
                    </template>
                </el-table-column> <% } else if (tableColumns[i].jsType==="date") { %>
                <!--日期类型-->
                <el-table-column
                    label="<%= tableColumns[i].colCNName %>"
                    show-overflow-tooltip
                >
                    <template #default="scope">
                        <span>{{scope.row.<%= tableColumns[i].colName %>| audaqueDatetime}}</span>
                    </template>
                </el-table-column> <% } else { %>
                <!--其它类型-->
                <el-table-column
                    prop="<%= tableColumns[i].colName %>"
                    label="<%= tableColumns[i].colCNName %>"
                    show-overflow-tooltip
                ></el-table-column> <% } %>  <% } %> <% if (optionButtons.length>0) { %>
                <el-table-column
                    v-if="audaqueHandlePermissionMixin([audaqueIconTypeMixin.update, audaqueIconTypeMixin.delete])"
                    :width="audaqueOpreateWidthMixin"
                    label="操作"
                >
                    <template slot-scope="scope"> <% for(var i=0; i<optionButtons.length;i++) { %> <% if ( optionButtons[i]==="编辑")  { %>
                        <audaque-link-text
                            v-audaque-permission="{ key: audaqueIconTypeMixin.update, list: audaquePermissionMixin }"
                            @click="audaqueGotoEditPageMixin(scope.row, audaqueFormTypeMixin.edit)"
                            iconClass="main-edit"
                            title="编辑"
                        ></audaque-link-text> <% } %> <% if ( optionButtons[i]==="删除" ) { %>
                        <audaque-link-text
                            v-audaque-permission="{ key: audaqueIconTypeMixin.delete, list: audaquePermissionMixin }"
                            @click="audaqueDeleteMixin(scope.row)"
                            iconClass="main-delete"
                            title="删除"
                        ></audaque-link-text> <% } %> <% } %>
                    </template>
                </el-table-column> <% } %>
            </audaque-table>
            <audaque-pagination
                @size-change="audaqueOnSizeChangeMixin"
                @current-change="audaqueOnCurrentChangeMixin"
                :current-page="audaquePageRequestMixin.pageNo"
                :page-size="audaquePageRequestMixin.pageSize"
                :total="audaqueDataListMixin.total"
            ></audaque-pagination>
        </template>
    </audaque-table-list-page>
</template>
<script>

import { <%= className %>List, delete<%= ClassName %> } from '@/api/config/<%= className %>';
import {
    audaqueDeleteItemMixin,
    audaqueListSearchMixin,
    audaqueList2FormMixin,
    audaqueConstantMixin,
    audaquePermissionMixin
} from '@audaque/vue-utils';

export default {
    mixins: [
        audaqueDeleteItemMixin(delete<%= ClassName %>), // 删除网络请求
        audaqueListSearchMixin(<%= className %>List), // 列表网络请求
        audaqueList2FormMixin,
        audaqueConstantMixin,
        audaquePermissionMixin
    ],

    data() {
        return {
            audaqueFormEditFormUrlMixin: '/config/<%= className %>/<%= className %>Edit', //详情页面路由
            selectionList: [], //选中的数据 <% for(var i in options) { %>
            <%= i %>: [<% for(var j=0;j<options[i].length;j++){ %>
                { name: <%=options[i][j].name %>, value: <%=options[i][j].value %> },<% } %>
            ], <% } %>
        };
    },

    components: {},

    watch: {},

    computed: {},

    created() {
    },

    mounted() {},

    methods: {
    <% if(selection) { %>
        handleSelectionChange(selection) {
            this.selectionList = selection;
            console.log('selectionList=', this.selectionList);
        },
    <% } %>
        getEnumLabel(arr,value){
            //arr 为数组 {name,value}
            for (let i = 0; i < arr.length; i++) {
                if (arr[i].value === value) {
                    return arr[i].name;
                }
            }
            return '-'
        }
    },

    beforeDestroy() {}
};
</script>

<style lang="scss" scoped></style>