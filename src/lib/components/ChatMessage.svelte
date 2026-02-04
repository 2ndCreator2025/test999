<script lang="ts">
  import { marked } from 'marked';
  import { User, Bot } from 'lucide-svelte';

  let { role, content } = $props<{ role: 'user' | 'assistant'; content: string }>();

  let htmlContent = $derived(marked.parse(content));
</script>

<div class="flex w-full gap-4 p-4 {role === 'assistant' ? 'bg-muted/50' : ''}">
  <div class="flex h-8 w-8 shrink-0 select-none items-center justify-center rounded-md border shadow-sm">
    {#if role === 'user'}
      <User class="h-4 w-4" />
    {:else}
      <Bot class="h-4 w-4" />
    {/if}
  </div>
  <div class="flex-1 overflow-hidden">
    <div class="prose dark:prose-invert break-words">
      {@html htmlContent}
    </div>
  </div>
</div>
