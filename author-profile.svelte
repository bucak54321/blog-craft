<script>
  import { supabase } from '../../supabase';
  import { onMount } from 'svelte';
  export let params;
  let authorPosts = [];
  let authorName = '';

  const getAuthorPosts = async () => {
    const { data, error } = await supabase
      .from('posts')
      .select('*')
      .eq('author', params.username)
      .order('created_at', { ascending: false });
    
    if (error) {
      console.error(error);
    } else {
      authorPosts = data;
      if (authorPosts.length > 0) {
        authorName = authorPosts[0].author;
      }
    }
  };

  onMount(() => {
    getAuthorPosts();
  });
</script>

<h1 class="text-3xl font-bold mb-4">Posts by {authorName}</h1>

{#if authorPosts.length > 0}
  <div class="space-y-4">
    {#each authorPosts as post}
      <div class="border-b pb-4">
        <a href={`/posts/${post.slug}`} class="text-xl font-semibold">{post.title}</a>
        <p class="text-sm text-gray-500">{post.created_at}</p>
        <div class="text-sm mt-2">{post.excerpt}</div>
      </div>
    {/each}
  </div>
{:else}
  <p>No posts available for this author.</p>
{/if}
