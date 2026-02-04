<script lang="ts">
  import { X } from 'lucide-svelte';

  let { open, apiKey, onSave, onClose } = $props<{
    open: boolean;
    apiKey: string;
    onSave: (key: string) => void;
    onClose: () => void;
  }>();

  let key = $state(apiKey);

  $effect(() => {
    key = apiKey;
  });
</script>

{#if open}
  <div class="fixed inset-0 z-50 flex items-center justify-center bg-background/80 backdrop-blur-sm">
    <div class="fixed left-[50%] top-[50%] z-50 grid w-full max-w-lg translate-x-[-50%] translate-y-[-50%] gap-4 border bg-background p-6 shadow-lg duration-200 sm:rounded-lg">
      <div class="flex flex-col space-y-1.5 text-center sm:text-left">
        <h2 class="text-lg font-semibold leading-none tracking-tight">Settings</h2>
        <p class="text-sm text-muted-foreground">
          Enter your OpenAI API Key to start chatting.
        </p>
      </div>
      <div class="grid gap-4 py-4">
        <div class="grid grid-cols-4 items-center gap-4">
          <label for="apikey" class="text-right text-sm font-medium">
            API Key
          </label>
          <input
            id="apikey"
            bind:value={key}
            class="col-span-3 flex h-9 w-full rounded-md border border-input bg-transparent px-3 py-1 text-sm shadow-sm transition-colors file:border-0 file:bg-transparent file:text-sm file:font-medium placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:cursor-not-allowed disabled:opacity-50"
          />
        </div>
      </div>
      <div class="flex flex-col-reverse sm:flex-row sm:justify-end sm:space-x-2">
        <button
            onclick={onClose}
            class="inline-flex h-9 items-center justify-center rounded-md border border-input bg-background px-4 py-2 text-sm font-medium shadow-sm transition-colors hover:bg-accent hover:text-accent-foreground focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50"
        >
            Cancel
        </button>
        <button
          onclick={() => onSave(key)}
          class="inline-flex h-9 items-center justify-center rounded-md bg-primary px-4 py-2 text-sm font-medium text-primary-foreground shadow transition-colors hover:bg-primary/90 focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50"
        >
          Save changes
        </button>
      </div>
      <button
        onclick={onClose}
        class="absolute right-4 top-4 rounded-sm opacity-70 ring-offset-background transition-opacity hover:opacity-100 focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2 disabled:pointer-events-none"
      >
        <X class="h-4 w-4" />
        <span class="sr-only">Close</span>
      </button>
    </div>
  </div>
{/if}
