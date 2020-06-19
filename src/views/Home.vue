<template>
    <div class="home">

        <div class="page-top">
            <div class="page-content">
                <h2>任务列表</h2>
            </div>
        </div>

        <div class="main">
            <div class="add-task">
                <h3 class="big-title">添加任务：</h3>
                <input placeholder="输入任务名称， 点回车即可添加任务" class="task-input" type="text" @keyup.enter="enterFn" v-model="todo" />
            </div>

            <div class="task-main">

                <ul class="task-nav">
                    <li v-for="(obj, ind) in nav" :key="'nav'+ind" :class="[chooseNav==ind?'chosStyle':'notChosStyle']" @click="changeNav(ind)">
                        {{ obj.title }}
                        <span>（{{ taskLenght(obj.mark) }}）</span>
                    </li>
                </ul>

                <div class="task-list">
                    <div class="tasks">
                        <span v-if="!list.length" class="no-task-tip">还没有添加任何任务哦！！</span>

                        <template v-else>
                            <div v-for="(item,index) in filterData" :key="'todo'+index">
                                <taskItem :item="item" :index="index" @edtorTaskState="edtorTaskState" @delTask="delTask"></taskItem>
                            </div>
                        </template>
                    </div>
                </div>
            </div>

        </div>
    </div>
</template>

<script>
    import taskItem from '../components/taskItem'
    //存取localStorage中的数据
    var store = {
        set(key, value) {
            window.localStorage.setItem(key, JSON.stringify(value));
        },
        get(key) {
            return JSON.parse(window.localStorage.getItem(key)) || [];
        }
    }

    export default {
        name: 'Home',
        components: { taskItem },
        data() {
            return {
                // nav array
                nav: [
                    { title: '未完成任务', mark: 'not' },
                    { title: '已完成任务', mark: 'is' },
                    { title: '全部任务', mark: 'all' },
                ],
                // 选中的nav下标
                chooseNav: 0,
                // 任务列表
                list: store.get("storeData"),
                // 输入的任务名
                todo: ''
            }
        },
        computed: {
            // 根据当前选中的nav下标 筛选对应数据
            filterData() {
                const { list } = this;
                let data = [];
                switch (this.chooseNav) {
                    case 0: // 返回未完成任务
                        data = list.filter(item => { return !item.isComplete; }) || list
                        break;

                    case 1: // 返回已完成任务
                        data = list.filter(item => { return item.isComplete; }) || list
                        break;

                    case 2: // 返回全部任务
                        data = list;
                        break;
                    default:
                        break;
                }
                return data;
            }

        },
        watch: {
            // 每次list有变动，就存到localStorage中，防止刷新数据重置
            list: {
                handler() {
                    store.set("storeData", this.list);
                },
                deep: true
            }
        },
        methods: {
            // 切换tab
            changeNav(index) {
                this.chooseNav = index;
            },

            /** 修改任务状态 */
            edtorTaskState(data) {
                console.log(data);
                data.item.isComplete = !data.item.isComplete;
            },

            /** 删除一项任务 */
            delTask(data) {
                console.log(data);
                var index = this.list.indexOf(data.item);
                this.list.splice(index, 1)
            },

            /** 向list中添加一项任务 */
            enterFn(e) {
                console.log(e);
                if (this.todo == "") {
                    return;
                }

                // 任务列表头部 塞入一条数据
                this.list.unshift({
                    title: this.todo,
                    isComplete: false
                });

                // 添加任务后  清空输入值
                this.todo = "";
            },

            /** 返回当前nav中 任务个数 */
            taskLenght(mark) {
                let len = 0;
                switch (mark) {
                    // 未完成的任务个数
                    case 'not':
                        len = this.list.filter(item => { return !item.isComplete; }).length
                        break;
                        // 已完成的任务个数
                    case 'is':
                        len = this.list.filter(item => { return item.isComplete; }).length
                        break;

                    case 'all':
                        len = this.list.length
                        break;
                    default:
                        break;
                }
                return len
            },
        },
    }
</script>

<style scope lang="scss">
    body {
        margin: 0;
        background-color: #fafafa;
        font: 14px 'Helvetica Neue', Helvetica, Arial, sans-serif;
    }

    .home {
        width: 80%;
        height: 620px;
        margin: 30px auto 0;
        border-radius: 10px;
        box-shadow: 0 0 10px #c1c1c1;
        overflow: hidden;


        .page-top {
            width: 100%;
            height: 40px;
            background-color: #3f9cdb;

            .page-content {
                width: 50%;
                margin: 0 auto;

                h2 {
                    margin: 0;
                    line-height: 40px;
                    font-size: 18px;
                    color: #fff;
                }
            }
        }

        .main {
            width: 90%;
            margin: 0 auto;
            box-sizing: border-box;

            .add-task {
                display: flex;
                justify-content: space-between;
                flex-direction: row;
                align-items: center;
                padding: 40px 0;
            }

            .big-title {
                min-width: 100px;
                color: #222;
            }

            .task-input {
                width: 99%;
                height: 30px;
                outline: 0;
                padding: 0 10px;
                border: 1px solid #ccc;
            }

            .task-main {
                display: flex;
                flex-direction: row;
                justify-content: space-between;
                align-items: flex-start;

                .task-nav {
                    padding: 0;
                    margin: 0;
                    list-style: none;

                    li {
                        width: 150px;
                        margin: 0 0 30px 0;
                        border-radius: 3px;
                        height: 40px;
                        line-height: 40px;
                        font-weight: bold;
                        font-size: 14px;

                        span {
                            font-size: 12px;
                        }
                    }

                    .chosStyle {
                        color: #ffffff;
                        background-color: #3f9cdb;
                    }

                    .notChosStyle {
                        color: #3f9cdb;
                        background-color: #ffffff;

                    }
                }

                .task-list {
                    display: flex;
                    flex: 1;
                    flex-direction: column;
                    background: #f3f3f3;
                    padding: 10px;
                    border-radius: 10px;
                    margin-left: 20px;
                    height: 400px;
                    overflow-y: scroll;

                    li {
                        position: relative;
                        font-size: 16px;
                        border-bottom: 1px solid #ededed;

                        &:hover {
                            background-color: orange;
                        }
                    }

                    .tasks {
                        width: 100%;
                        height: 400px;
                        background-color: #fff;
                        overflow-y: scroll;
                    }
                }

                .no-task-tip {
                    padding: 10px 0 10px 10px;
                    display: block;
                    border-bottom: 1px solid #ededed;
                    color: #777;
                }

            }
        }

    }
</style>