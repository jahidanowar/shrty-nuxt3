<script lang="ts" setup>
import geoip from "geoip-lite";
import { Database } from "~~/types/supabase";

const params = useRoute().params;
const client = useSupabaseClient<Database>();

// Get the params id
if (!params.id) {
  throw createError({
    statusCode: 404,
    message: "Not found",
  });
}
// Fetch the data from the supabase
const { data } = await useAsyncData("link", async () => {
  const { data, error } = await client
    .from("links")
    .select("*")
    .eq("key", params.id)
    .single();

  if (error) {
    throw createError({
      statusCode: 404,
      message: "Not found",
    });
  }

  return data;
});

if (data.value?.long_url) {
  const ua = useUserAgent();
  if (ua && ua.ip) {
    const geoLocation = geoip.lookup(ua.ip);
    const { data: res, error } = await client.from("clicks").insert({
      link_id: data.value.id,
      ip: ua.ip,
      country: geoLocation?.country,
      city: geoLocation?.city,
      user_agent: ua.userAgent,
    });
  }
  useExtranalRedirect(data.value?.long_url);
}

// Redirect to the long url
</script>

<template>
  <div class="pt-[200px]"></div>
</template>
