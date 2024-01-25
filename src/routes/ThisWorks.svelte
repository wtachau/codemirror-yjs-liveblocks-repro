<script lang="ts">
  import LiveblocksProvider from "@liveblocks/yjs";
  import { yCollab } from "y-codemirror.next";
  import { EditorView } from "codemirror";
  import { EditorState } from "@codemirror/state";
  import * as Y from "yjs";
  import { onMount } from "svelte";
  import type { Client } from "@liveblocks/client";

  export let client: Client;

  let parentElement: HTMLElement | undefined;

  const yDoc = new Y.Doc();
  const yText = yDoc.getText("codemirror");

  const ROOM_NAME = "codemirror-yjs-example-works";

  onMount(() => {
    const { room, leave } = client.enterRoom(ROOM_NAME, {
      initialPresence: {},
    });

    const liveblocksProvider = new LiveblocksProvider(room, yDoc);

    const state = EditorState.create({
      doc: "",
      extensions: [yCollab(yText, null)],
    });

    // Attach CodeMirror to element
    const view = new EditorView({
      state,
      parent: parentElement,
    });

    return () => {
      leave();
      view.destroy();
    };
  });
</script>

<div class="container" bind:this={parentElement} />

<style lang="postcss">
  .container {
    border: 1px solid blue;
  }
</style>
