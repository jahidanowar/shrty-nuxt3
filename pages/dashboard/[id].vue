<script lang="ts" setup>
import { Database } from "~~/types/supabase";

const { id } = useRoute().params;

const { data } = useAsyncData(id.toString(), async () => {
  const client = useSupabaseClient<Database>();

  const { data: res, error } = await client
    .from("links")
    .select("*, clicks(*)")
    .eq("key", id)
    .single();
  return res;
});
</script>

<template>
  <main v-if="data" class="pt-20">
    <div class="container">
      <h1 class="text-5xl text-amber-500 font-bold">/{{ data.key }}</h1>
      <div class="mt-5">{{ data.created_at }}</div>
      <div class="mt-5 flex gap-5">
        <div class="card">
          <div class="text-2xl text-white/40 overflow-x-auto" texteditable>
            {{ data.long_url }}
          </div>
        </div>
        <div class="card">
          <div class="text-6xl font-bold text-white">
            {{ data.total_clicks }}
          </div>
          <div>Total Clicks</div>
        </div>
      </div>
    </div>

    <div class="container mt-10">
      <h2 class="text-2xl mb-5">Clicks Log</h2>
      <div v-for="click in data.clicks" class="card mb-5">
        <div
          v-if="click && typeof click === 'object'"
          class="flex justify-between items-center"
        >
          <div>
            <div class="text-lg font-bold text-amber-500">{{ click.ip }}</div>
            {{ click.created_at }}
          </div>
          <div>
            {{ click.country }}
            {{ click.city }}
          </div>
        </div>
      </div>
    </div>
  </main>
</template>
