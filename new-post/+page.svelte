<script>
  import { supabase } from '../../../supabase';
  import { onMount } from 'svelte';
  import { marked } from 'marked';

  export let params;
  let post = null;

  const getPost = async () => {
    const { data, error } = await supabase
      .from('posts')
      .select('*')
      .eq('slug', params.slug)
      .single();

    if (error) {
      console.log('Error fetching post:', error.message);
    } else {
      post = data;
    }
  };

  onMount(getPost);
</script>

{#if post}
  <div class="prose">
    <h1 class="text-4xl font-semibold">{post.title}</h1>
    <p class="text-sm text-gray-500">{post.created_at}</p>
    <div class="mt-6" bind:this={postContent}>
      {@html marked(post.content)}
    </div>
  </div>
{:else}
  <p>Loading...</p>
{/if}
