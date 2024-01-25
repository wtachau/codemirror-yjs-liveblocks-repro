<script lang="ts">
  import ThisWorks from "./ThisWorks.svelte";
  import ThisDoesntWork from "./ThisDoesntWork.svelte";
  import { createClient } from "@liveblocks/client";

  const PUBLIC_KEY =
    "pk_dev_dwrdVDEsVj5ek1SMrx4UGb0bg_FvkICIXKU3yRViu78JVdwVzb9MFf9ME2CWFr3r";

  const client = createClient({ publicApiKey: PUBLIC_KEY });
</script>

this works:
<ThisWorks {client} />

<hr />

this doesn't work:
<ThisDoesntWork {client} />
