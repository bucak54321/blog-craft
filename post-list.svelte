<script>
  import { supabase } from '../../supabase';
  import { onMount } from 'svelte';

  let posts = [];
  let tags = [];
  let selectedTag = '';
  let currentPage = 1;
  const postsPerPage = 5;

  const getTags = async () => {
    const { data, error } = await supabase
      .from('posts')
      .select('tags')
      .distinct();
    if (error) {
      console.error(error);
    } else {
      tags = data.map(item => item.tags).flat();
    }
  };

  const getPosts = async () => {
    const { data, error } = await supabase
      .from('posts')
      .select('*')
      .ilike('tags', `%${selectedTag}%`)
      .range((currentPage - 1) * postsPerPage, currentPage * postsPerPage - 1)
      .order('created_at', { ascending: false });
    
    if (error) {
      console.error(error);
    } else {
      posts = data;
    }
  };

  const handleTagChange = (tag) => {
    selectedTag = tag;
    currentPage = 1;
    getPosts();
  };

  const handlePagination = (page) => {
    currentPage = page;
    getPosts();
  };

  onMount(() => {
    getTags();
    getPosts();
  });
</script>

<h1 class="text-3xl font-bold mb-4">All Posts</h1>

<div class="mb-4">
  <label for="tags" class="block">Filter by Tag</label>
  <select id="tags" bind:value={selectedTag} on:change={() => handleTagChange(selectedTag)} class="p-2 rounded">
    <option value="">All Tags</option>
    {#each tags as tag}
      <option value={tag}>{tag}</option>
    {/each}
  </select>
</div>

<div class="space-y-4">
  {#each posts as post}
    <div class="border-b pb-4">
      <a href={`/posts/${post.slug}`} class="text-xl font-semibold">{post.title}</a>
      <p class="text-sm text-gray-500">{post.created_at}</p>
      <div class="text-sm mt-2">{post.excerpt}</div>
    </div>
  {/each}
</div>

<div class="mt-6 flex justify-center space-x-4">
  <button on:click={() => handlePagination(currentPage - 1)} disabled={currentPage === 1} class="bg-blue-500 text-white p-2 rounded">Previous</button>
  <button on:click={() => handlePagination(currentPage + 1)} class="bg-blue-500 text-white p-2 rounded">Next</button>
</div>
