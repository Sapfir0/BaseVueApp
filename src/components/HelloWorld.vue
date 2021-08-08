<template>
    <div class="hello">
        <h1>{{ msg }}</h1>
        <p>
            {{ post }}
        </p>
    </div>
</template>

<script lang="ts">
import container from '@/inversify/inversifyContainer';
import { SERVICE_IDENTIFIER } from '@/inversify/inversifyTypes';
import { ApiInteractionService, BaseInteractionError } from 'api_interaction_services';
import { isRight } from 'fp-ts/lib/Either';
import { defineComponent } from 'vue';

declare interface HelloWorld {
    post: string | null;
    err: BaseInteractionError | null;
    fetcher: ApiInteractionService;
}

export default defineComponent({
    name: 'HelloWorld',
    props: {
        msg: String,
    },
    data(): HelloWorld {
        return {
            post: null,
            err: null,
            fetcher: container.get<ApiInteractionService>(SERVICE_IDENTIFIER.ApiInteractionService),
        };
    },
    async created() {
        const res = await this.fetcher.get<string>('/random');
        if (isRight(res)) {
            this.post = res.right;
        } else {
            this.err = res.left;
        }
    },
});
</script>
<style scoped lang="scss">
h3 {
    margin: 40px 0 0;
}
ul {
    list-style-type: none;
    padding: 0;
}
li {
    display: inline-block;
    margin: 0 10px;
}
a {
    color: #42b983;
}
</style>
