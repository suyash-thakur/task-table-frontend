<template>
    <div class="container">
        <a-row type="flex" justify="start" align="top">
            <a-col :span="6" v-for="(card, i) in cards" :key="card._id" class="state">
                <div class="header">
                    <div>
                        <a-tag color="pink" v-if="card.title === 'Not Started'">
                            {{card.title}}
                        </a-tag>
                        <a-tag color="orange" v-if="card.title === 'In Progress'">
                            {{card.title}}
                        </a-tag>
                        <a-tag color="green" v-if="card.title === 'Completed'">
                            {{card.title}}
                        </a-tag>
                        <a-tag color="green" v-if="card.title !== 'Completed' && card.title !== 'In Progress' &&  card.title !== 'Not Started'">
                            {{card.title}}
                        </a-tag>
                        <a-tag>
                            {{card.cards.length}}
                        </a-tag>
                    </div>
                    <div>
                        <a-icon type="dash" />
                        <a-icon type="plus" />
                    </div>
                </div>
                <Container group-name="1" @drop="onDrop(card._id, $event)" :get-child-payload="getChildPayload1" v-if="i === 0">
                    <Draggable v-for="task in card.cards" :key="task._id">
                        <div>
                            <a-card style="width: 100%; margin-top: 5px; padding: 5px; cursor: pointer;" :bodyStyle="styleObject" size="small" draggable>
                                <div style="width: 100%; display: flex; justify-content: space-between;">
                                    <p style="margin: 0px; font-weight: bold; cursor: pointer;">
                                        {{task.content}}
                                    </p>
                                    <div>
                                        <a-icon type="edit" @click="showModalTask(task._id, card._id, task.content)" />
                                        &nbsp;
                                        <a-icon type="delete" @click="removeTask(card._id,task._id )" />
                                    </div>
                                </div>
                            </a-card>
                        </div>
                    </Draggable>
                    <div class="footer">
                        <a-icon type="plus" style="cursor: pointer;" @click="showModal(card._id)" />
                        <div style="margin-left: 10px; cursor: pointer;" @click="showModal(card._id)">
                            New
                        </div>
                    </div>
                </Container>
                <Container group-name="1" @drop="onDrop(card._id, $event)" :get-child-payload="getChildPayload2" v-if="i === 1">
                    <Draggable v-for="task in card.cards" :key="task._id">
                        <div>
                            <a-card style="width: 100%; margin-top: 5px; padding: 5px; cursor: pointer;" :bodyStyle="styleObject" size="small" draggable>
                                <div style="width: 100%; display: flex; justify-content: space-between;">
                                    <p style="margin: 0px; font-weight: bold; cursor: pointer;">
                                        {{task.content}}
                                    </p>
                                    <div>
                                        <a-icon type="edit" @click="showModalTask(task._id, card._id, task.content)" />
                                        &nbsp;
                                        <a-icon type="delete" @click="removeTask(card._id,task._id )" />
                                    </div>
                                </div>
                            </a-card>
                        </div>
                    </Draggable>
                    <div class="footer">
                        <a-icon type="plus" style="cursor: pointer;" @click="showModal(card._id)" />
                        <div style="margin-left: 10px; cursor: pointer;" @click="showModal(card._id)">
                            New
                        </div>
                    </div>
                </Container>
                <Container group-name="1" @drop="onDrop(card._id, $event)" :get-child-payload="getChildPayload3" v-if="i === 2">
                    <Draggable v-for="task in card.cards" :key="task._id">
                        <div>
                            <a-card style="width: 100%; margin-top: 5px; padding: 5px; cursor: pointer;" :bodyStyle="styleObject" size="small" draggable>
                                <div style="width: 100%; display: flex; justify-content: space-between;">
                                    <p style="margin: 0px; font-weight: bold; cursor: pointer;">
                                        {{task.content}}
                                    </p>
                                    <div>
                                        <a-icon type="edit" @click="showModalTask(task._id, card._id, task.content)" />
                                        &nbsp;
                                        <a-icon type="delete" @click="removeTask(card._id,task._id )" />
                                    </div>
                                </div>
                            </a-card>
                        </div>
                    </Draggable>
                    <div class="footer">
                        <a-icon type="plus" style="cursor: pointer;" @click="showModal(card._id)" />
                        <div style="margin-left: 10px; cursor: pointer;" @click="showModal(card._id)">
                            New
                        </div>
                    </div>
                </Container>
            </a-col>
        </a-row>
        <a-modal v-model="visible" title="Add New Task" @ok="handleOk">
            <a-form :form="form" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }" @submit="handleSubmit">
                <a-form-item label="Task">
                    <a-input v-decorator="['Task', { rules: [{ required: true, message: 'Please input your task!' }] }]" />
                </a-form-item>
                <a-form-item :wrapper-col="{ span: 12, offset: 5 }">
                    <a-button type="primary" html-type="submit"> Submit </a-button>
                </a-form-item>
            </a-form>
        </a-modal>
        <a-modal v-model="visible_task" title="Edit Task" @ok="handleOk">
            <a-form :form="formTask" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }" @submit="handleSubmitTask">
                <a-form-item label="Task">
                    <a-input v-decorator="['TaskData', { initialValue: task_content, rules: [{ required: true, message: 'Please input your task!' }],  }]" />
                </a-form-item>
                <a-form-item :wrapper-col="{ span: 12, offset: 5 }">
                    <a-button type="primary" html-type="submit"> Submit </a-button>
                </a-form-item>
            </a-form>
        </a-modal>
    </div>
</template>

<script>
    import axios from "axios";
    import { Container, Draggable } from "vue-smooth-dnd";
    export default {
        components: { Container, Draggable },
        data() {
            return {
                visible: false,
                visible_task: false,
                formLayout: "horizontal",
                form: this.$form.createForm(this, { name: "coordinated" }),
                formTask: this.$form.createForm(this, { name: "coordinatedTask" }),
  cards:[],
                selected_id: "",
                selected_task_id: "",
                styleObject: {
                    padding: "5px",
                },
                task_content: "Test Content",
            };
        },
        async asyncData() {
            const cards = await axios.get("https://task-table-backend.herokuapp.com/status").then((response) => {
              console.log(response.data);
                return response.data;
            });
            return { cards };
        },

        methods: {
            onDrop(item, dropResult) {
                let dropItem, dropTask, dragTask;
                dropResult.payload.task = JSON.parse(JSON.stringify(dropResult.payload.task));
                if (dropResult.addedIndex !== null) {
                    this.cards.forEach((card) => {
                        if (card._id == dropResult.payload.hostid) {
                            card.cards = card.cards.filter((item) => item._id !== dropResult.payload.task._id);
                        }
                        if (card._id == item) {
                            card.cards.push(dropResult.payload.task);
                        }
                    });
                    axios
                        .request({
                            method: "PUT",
                            url: `https://task-table-backend.herokuapp.com/dragTask`,
                            headers: {},
                            data: {
                                host_status_id: dropResult.payload.hostid,
                                task_id: dropResult.payload.task._id,
                                target_status_id: item,
                            },
                        })
                        .then((response) => {});
                }
            },
            handleSubmit(e) {
                e.preventDefault();
                this.form.validateFields((err, values) => {
                    if (!err) {
                        axios
                            .request({
                                method: "POST",
                                url: `https://task-table-backend.herokuapp.com/task`,
                                headers: {},
                                data: {
                                    status_id: this.selected_id,
                                    content: values.Task,
                                },
                            })
                            .then((response) => {
                                this.visible = false;
                                for (let i = 0; i < this.cards.length; i++) {
                                    if (this.cards[i]._id == response.data.status._id) {
                                        this.cards[i] = response.data.status;
                                    }
                                }
                            });
                    }
                });
            },
            handleSubmitTask(e) {
                e.preventDefault();
                this.formTask.validateFields((err, values) => {
                    if (!err) {
                        axios
                            .request({
                                method: "PUT",
                                url: `https://task-table-backend.herokuapp.com/updateTask/${this.selected_task_id}`,
                                headers: {},
                                data: {
                                    content: values.TaskData,
                                },
                            })
                            .then((response) => {
                                this.visible_task = false;
                                for (let i = 0; i < this.cards.length; i++) {
                                    if (this.cards[i]._id == this.selected_id) {
                                        for (let j = 0; j < this.cards[i].cards.length; j++) {
                                            if (this.cards[i].cards[j]._id == this.selected_task_id) {
                                                this.cards[i].cards[j].content = values.TaskData;
                                            }
                                        }
                                    }
                                }
                            });
                    }
                });
            },
            getChildPayload1(index) {
                return { hostid: this.cards[0]._id, task: this.cards[0].cards[index] };
            },
            getChildPayload2(index) {
                return { hostid: this.cards[1]._id, task: this.cards[1].cards[index] };
            },
            getChildPayload3(index) {
                return { hostid: this.cards[2]._id, task: this.cards[2].cards[index] };
            },
            showModal(id) {
                this.visible = true;
                this.selected_id = id;
            },
            showModalTask(id, state_id, content) {
                this.visible_task = true;
                this.selected_task_id = id;
                this.selected_id = state_id;
                this.task_content = content;
            },
            handleOkTask(e) {
                this.visible_task = false;
            },
            handleOk(e) {
                this.visible = false;
            },
            editTask(task_id, state_id, content) {},
            removeTask(status_id, task_id) {
                axios
                    .request({
                        method: "PUT",
                        url: `https://task-table-backend.herokuapp.com/removeTask`,
                        headers: {},
                        data: {
                            status_id: status_id,
                            task_id: task_id,
                        },
                    })
                    .then((response) => {
                        for (let i = 0; i < this.cards.length; i++) {
                            if (this.cards[i]._id === status_id) {
                                this.cards[i].cards = this.cards[i].cards.filter((task) => task._id !== task_id);
                            }
                        }
                    });
            },
        },
    };
</script>
<style scoped>
    .container {
        padding: 10%;
    }
    .header {
        width: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
    .ant-card-small > .ant-card-body {
        padding: 8px !important;
    }

    .state {
        margin: 10px;
    }
    .footer {
        display: flex;
        height: 80px;
        align-items: center;
    }
</style>
