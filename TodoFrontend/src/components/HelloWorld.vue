<template>
    <div class="post">
        <div v-if="loading" class="loading">
            Loading... Please refresh once the ASP.NET backend has started. See <a href="https://aka.ms/jspsintegrationvue">https://aka.ms/jspsintegrationvue</a> for more details.
        </div>

        <div v-if="post" class="content">
            <table>
                <thead>
                    <tr>
                        <th>Id</th>
                        <th>Name</th>
                        <th>IsComplete</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="todo in post" :key="todo.id">
                        <td>{{ todo.id }}</td>
                        <td>{{ todo.name }}</td>
                        <td>{{ todo.isComplete }}</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script lang="ts">
    import { defineComponent } from 'vue';

    type Todo = {
        Id: number;
        Name: string;
        IsComplete: boolean;
    }[];

    interface Data {
        loading: boolean,
        post: null | Todo
    }

    export default defineComponent({
        data(): Data {
            return {
                loading: false,
                post: null
            };
        },
        created() {
            // fetch the data when the view is created and the data is
            // already being observed
            this.fetchData();
        },
        watch: {
            // call again the method if the route changes
            '$route': 'fetchData'
        },
        methods: {
            fetchData(): void {
                this.post = null;
                this.loading = true;

                fetch('/api/todoitems/')
                    .then(r => r.json())
                    .then(json => {
                        console.log(json);
                        this.post = json as Todo;
                        this.loading = false;
                        return;
                    });
            }
        },
    });
</script>