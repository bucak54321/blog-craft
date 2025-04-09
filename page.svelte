import { onMount } from 'svelte';
  import { supabase } from '../supabase';
  let posts = [];

  onMount(async () => {
    let { data } = await supabase.from('posts').select('*').order('created_at', { ascending: false });
    posts = data;
  });
</script>

<h1 class="text-4xl font-bold mb-6">Blog Craft</h1>
<ul class="space-y-4">
  {#each posts as post}
    <li>
      <a class="text-xl font-semibold text-blue-500 hover:underline" href={`/post/${post.slug}`}>{post.title}</a>
      <p class="text-sm text-gray-600">{post.tags.join(', ')}</p>
    </li>
  {/each}
</ul>
