<script lang="ts">
  import { onMount } from 'svelte';
  import { Settings } from 'lucide-svelte';
  import ChatMessage from '$lib/components/ChatMessage.svelte';
  import ChatInput from '$lib/components/ChatInput.svelte';
  import SettingsDialog from '$lib/components/SettingsDialog.svelte';

  let messages = $state<{ role: 'user' | 'assistant'; content: string }[]>([]);
  let apiKey = $state('');
  let isLoading = $state(false);
  let showSettings = $state(false);

  onMount(() => {
    const storedKey = localStorage.getItem('openai_api_key');
    if (storedKey) {
      apiKey = storedKey;
    } else {
      showSettings = true;
    }
  });

  function saveApiKey(key: string) {
    apiKey = key;
    localStorage.setItem('openai_api_key', key);
    showSettings = false;
  }

  async function sendMessage(content: string) {
    if (!apiKey) {
      showSettings = true;
      return;
    }

    messages = [...messages, { role: 'user', content }];
    isLoading = true;

    try {
      const response = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
          model: 'gpt-3.5-turbo',
          messages: messages.map(m => ({ role: m.role, content: m.content })),
          stream: false // implementing simple fetch for now
        })
      });

      if (!response.ok) {
        throw new Error('API request failed');
      }

      const data = await response.json();
      const reply = data.choices[0].message.content;

      messages = [...messages, { role: 'assistant', content: reply }];
    } catch (error) {
      console.error(error);
      messages = [...messages, { role: 'assistant', content: 'Error: Failed to fetch response. Check your API Key.' }];
    } finally {
      isLoading = false;
    }
  }
</script>

<div class="flex h-screen flex-col bg-background text-foreground">
  <!-- Header -->
  <header class="flex h-14 items-center justify-between border-b px-4 lg:h-[60px]">
    <h1 class="text-xl font-bold">ChatGPT Clone</h1>
    <button
      onclick={() => (showSettings = true)}
      class="inline-flex h-9 w-9 items-center justify-center rounded-md border border-input bg-background shadow-sm hover:bg-accent hover:text-accent-foreground"
    >
      <Settings class="h-4 w-4" />
      <span class="sr-only">Settings</span>
    </button>
  </header>

  <!-- Chat Area -->
  <main class="flex-1 overflow-y-auto">
    <div class="mx-auto flex w-full max-w-3xl flex-col pb-20 pt-4">
      {#if messages.length === 0}
        <div class="flex h-full flex-col items-center justify-center p-8 text-center text-muted-foreground">
          <p class="text-lg font-medium">No messages yet.</p>
          <p class="text-sm">Start a conversation or configure your API key.</p>
        </div>
      {/if}
      {#each messages as msg}
        <ChatMessage role={msg.role} content={msg.content} />
      {/each}
      {#if isLoading}
        <div class="p-4 text-center text-sm text-muted-foreground">
          Thinking...
        </div>
      {/if}
    </div>
  </main>

  <!-- Input Area -->
  <div class="border-t bg-background p-4">
    <ChatInput onSend={sendMessage} disabled={isLoading} />
  </div>

  <SettingsDialog
    open={showSettings}
    apiKey={apiKey}
    onSave={saveApiKey}
    onClose={() => (showSettings = false)}
  />
</div>