<script>
  import { supabase } from '../../supabase';
  import { onMount } from 'svelte';
  export let params;
  let categoryPosts = [];

  const getCategoryPosts = async () => {
    const { data, error } = await supabase
      .from('posts')
      .select('*')
      .eq('category', params.category)
      .order('created_at', { ascending: false });

    if (error) {
      console.error(error);
    } else {
      categoryPosts = data;
    }
  };

  onMount(() => {
    getCategoryPosts();
  });
</script>

<h1 class="text-3xl font-bold mb-4">Posts in {params.category} Category</h1>

{#if categoryPosts.length > 0}
  <div class="space-y-4">
    {#each categoryPosts as post}
      <div class="border-b pb-4">
        <a href={`/posts/${post.slug}`} class="text-xl font-semibold">{post.title}</a>
        <p class="text-sm text-gray-500">{post.created_at}</p>
        <div class="text-sm mt-2">{post.excerpt}</div>
      </div>
    {/each}
  </div>
{:else}
  <p>No posts found in this category.</p>
{/if}
