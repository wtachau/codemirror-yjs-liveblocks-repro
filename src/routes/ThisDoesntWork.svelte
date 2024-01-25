<script lang="ts">
  import LiveblocksProvider from "@liveblocks/yjs";
  import { yCollab } from "y-codemirror.next";
  import { EditorView } from "codemirror";
  import { EditorState } from "@codemirror/state";
  import * as Y from "yjs";
  import { onMount } from "svelte";
  import { type Client } from "@liveblocks/client";

  export let client: Client;

  let subdoc1Element: HTMLElement | undefined;
  let subdoc2Element: HTMLElement | undefined;

  const yDoc = new Y.Doc({ autoLoad: true });

  const ROOM_NAME = "codemirror-yjs-example-doesnt-work";

  onMount(() => {
    const { room, leave } = client.enterRoom(ROOM_NAME, {
      initialPresence: {},
    });

    const liveblocksProvider = new LiveblocksProvider(room, yDoc, {
      autoloadSubdocs: true, // have also tried with 'false'
    });

    /*
     * The code below attempts to follow the example here:
     *  https://liveblocks.io/blog/liveblocks-1-6-introducing-yjs-subdocuments-support#Basic-usage-example
     */
    const subdoc1 = new Y.Doc();
    const subdoc2 = new Y.Doc();

    yDoc.getMap().set("subdoc1", subdoc1);
    yDoc.getMap().set("subdoc2", subdoc2);

    console.log("subdoc1 guid:", subdoc1.guid);
    console.log("subdoc2 guid:", subdoc2.guid);

    yDoc.on("subdocs", (subdocsEvent) => {
      console.log("subdocs event: ", subdocsEvent);
    });

    // If I understand correctly, these are redundant
    liveblocksProvider.loadSubdoc(subdoc1.guid);
    liveblocksProvider.loadSubdoc(subdoc2.guid);
    subdoc1.load();
    subdoc2.load();

    const yText1 = subdoc1.getText("codemirror");
    const yText2 = subdoc2.getText("codemirror");

    const state1 = EditorState.create({
      doc: "",
      extensions: [yCollab(yText1, null)],
    });

    const state2 = EditorState.create({
      doc: "",
      extensions: [yCollab(yText2, null)],
    });

    // Attach CodeMirror to element
    const view1 = new EditorView({
      state: state1,
      parent: subdoc1Element,
    });

    const view2 = new EditorView({
      state: state2,
      parent: subdoc2Element,
    });

    yText1.observe((event) => {
      console.log("event from yText1: ", event);
    });
    yText2.observe((event) => {
      console.log("event from yText2: ", event);
    });

    return () => {
      leave();
      view1.destroy();
      view2.destroy();
    };
  });
</script>

<div class="container">
  <div bind:this={subdoc1Element} />
  <div bind:this={subdoc2Element} />
</div>

<style lang="postcss">
  .container {
    border: 1px solid black;
  }
</style>
