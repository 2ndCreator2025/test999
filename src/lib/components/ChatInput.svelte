<script lang="ts">
  import { SendHorizontal } from 'lucide-svelte';

  let { onSend, disabled } = $props<{ onSend: (text: string) => void; disabled: boolean }>();
  let value = $state('');

  function handleSubmit(e?: Event) {
    e?.preventDefault();
    if (!value.trim() || disabled) return;
    onSend(value);
    value = '';
  }

  function onKeydown(e: KeyboardEvent) {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      handleSubmit();
    }
  }
</script>

<form onsubmit={handleSubmit} class="mx-auto flex w-full max-w-3xl items-end gap-2 p-4">
  <div class="relative flex w-full flex-1 items-center">
    <textarea
      bind:value
      onkeydown={onKeydown}
      placeholder="Message ChatGPT..."
      class="flex max-h-[200px] min-h-[60px] w-full resize-none rounded-md border border-input bg-transparent px-3 py-2 text-sm shadow-sm placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:cursor-not-allowed disabled:opacity-50"
      disabled={disabled}
    ></textarea>
  </div>
  <button
    type="submit"
    disabled={!value.trim() || disabled}
    class="inline-flex h-[60px] w-[60px] items-center justify-center rounded-md bg-primary text-primary-foreground shadow transition-colors hover:bg-primary/90 focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50"
  >
    <SendHorizontal class="h-5 w-5" />
    <span class="sr-only">Send</span>
  </button>
</form>
