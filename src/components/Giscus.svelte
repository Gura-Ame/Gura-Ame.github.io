<script>
import { onMount } from "svelte";

onMount(() => {
	const script = document.createElement("script");
	script.src = "https://giscus.app/client.js";
	script.setAttribute("data-repo", "Gura-Ame/Gura-Ame.github.io");
	script.setAttribute("data-repo-id", "R_kgDOO6h2aA");
	script.setAttribute("data-category", "Ideas");
	script.setAttribute("data-category-id", "DIC_kwDOO6h2aM4CsMBN");
	script.setAttribute("data-mapping", "pathname");
	script.setAttribute("data-strict", "1");
	script.setAttribute("data-reactions-enabled", "1");
	script.setAttribute("data-emit-metadata", "1");
	script.setAttribute("data-input-position", "top");
	script.setAttribute("data-theme", "preferred_color_scheme");
	script.setAttribute("data-lang", "en");
	script.setAttribute("data-loading", "lazy");
	script.setAttribute("crossorigin", "anonymous");
	script.async = true;

	document.getElementById("giscus-container")?.appendChild(script);
});
</script>

<div id="giscus-container"></div>
