<script lang="ts">
	import { onMount } from "svelte";
	import { goto } from "$app/navigation";
	import { dev } from "$app/environment";
  
	import { pwaBeforeInstallPrompt, canInstall } from "$lib/services/pwa";
	import { showMenu } from "$lib/services/menu";
  
	import { menuController, modalController, registerMenu } from "ionic-svelte";
	import { isPlatform } from "@ionic/core";
	import * as allIonicIcons from "ionicons/icons";
  
	import IOSInstall from "$lib/components/IOSInstall.svelte";
  
	//	let hideMenu = true; // a hack because the menu shows before the splash (in Chrome on Windows)
	$: hideMenu = !$showMenu;
  
	export let side: "start" | "end" | undefined = "start";
  
	let inlineModalOpen = false;
    
	// this is unfortunately needed in order to have the menuController API function properly
	onMount(() => {
	  registerMenu("mainmenu");
	});
  
	// and build the menu list from it
	//const modules = import.meta.glob("../../**/*.svelte", { as: "raw" });
  
	// console.log("MODULES", modules);
  
	// ChatGPT suggestion
	// let input = '../../routes/components/Card/+page.svelte';
	// let regex = /components\/(.+?)\//;
	// let match = input.match(regex);
	// console.log(match[1]); // outputs "Card"

	const menuItems = [{
		icon: allIonicIcons.airplaneOutline,
		label:"Tabs",
		url:"/components/tabs/explain"}
	]

	const closeAndNavigate = async (url: string) => {
	  // take the google tag from the main thread
	  setTimeout(() => {
		//@ts-ignore
		if (!dev) window.gtag("event", url);
	  }, 100);
  
	  console.log("Navigate url", url);
  
	  goto(url);
	  menuController.close("mainmenu");
	};
  
	// hack because of visibility of menu in Chrome/Windows
	setTimeout(() => {
	  //	hideMenu = false;
	}, 100);
  
	let iosInstall = isPlatform("ios") && !isPlatform("pwa");
  
	const showIOSinstall = async () => {
	  const modal = await modalController.create({
		component: IOSInstall,
		componentProps: {},
		showBackdrop: true,
		backdropDismiss: false,
	  });
  
	  modal.onDidDismiss().then((value) => {
		console.log("Dismissed the modal", value);
		if (value.role === "backdrop") console.log("Backdrop clicked");
	  });
  
	  await modal.present();
	};
  
	function capitalizeFirstLetter(text: string) {
	  return text.charAt(0).toUpperCase() + text.slice(1);
	}
  </script>
  
  <ion-menu {side} content-id="main" menu-id="mainmenu" class:menuhide={hideMenu}>
	<ion-modal is-open={inlineModalOpen}>
	  <br />
  
	  <ion-content>
		<ion-card>
		  <ion-card-header>
			<ion-card-title>Ionic Svelte - Unofficial Ionic integration</ion-card-title>
		  </ion-card-header>
		  <ion-card-content>
			<p>Ionic-Svelte is work in progress and needs your support.</p>
			<br />
			<p>
			  Share how you are using it, what is really working for you and which parts need
			  improvement.
			</p>
			<br />
			<p>
			  Do you like this? Star the project on Github - <a
				href="https://github.com/Tommertom/svelte-ionic-app"
				target="_new">https://github.com/Tommertom/svelte-ionic-app</a
			  >
			</p>
			<br />
			<p>
			  Join <a
				href="https://discordapp.com/channels/520266681499779082/1049388501629681675"
				target="_new">Ionic-Svelte channel</a
			  > on Ionic's official discord
			</p>
			<br /><br />
			<p>Thanks!!! Tommertom</p>
			<br />
			<img src="icon-128.png" width="25%" alt="Feedback" />
		  </ion-card-content>
		</ion-card>
		<br />
		<ion-button role="button" tabindex="0"
		  expand="block"
		  on:click={() => {
			inlineModalOpen = false;
		  }}
		>
		  Close modal
		</ion-button>
		vite v4.
	  </ion-content>
	</ion-modal>
  
	{#if menuItems.length > 0}
	  <ion-header>
		<ion-toolbar>
		  <ion-title>Menu</ion-title>
		</ion-toolbar>
	  </ion-header>
	  <ion-content>
		<ion-list>
		  {#each menuItems as menuItem, i}
			<ion-item role="menuitem" tabindex="0"
			  on:click={() => {
				closeAndNavigate(menuItem.url);
			  }}
			>
			  <ion-icon icon={menuItem.icon} slot="start" color="primary" />
			  <ion-label>{menuItem.label}</ion-label>
			</ion-item>
		  {/each}
  
		  <ion-item />
		  {#if iosInstall}
			<ion-item role="menuitem" tabindex="1"
			on:click={showIOSinstall}>
			  <ion-icon icon={allIonicIcons["download"]} slot="start" />
			  <ion-label>Install this app as PWA</ion-label>
			</ion-item>
			<ion-item />
		  {/if}
		  {#if $canInstall}
			<ion-item role="menuitem" tabindex="2"
			  on:click={() => {
				const prompt = $pwaBeforeInstallPrompt;
				prompt.prompt();
			  }}
			>
			  <ion-icon icon={allIonicIcons["download"]} slot="start" />
			  <ion-label>Install this app as PWA</ion-label>
			</ion-item>
			<ion-item />
		  {/if}
  
		</ion-list>
	  </ion-content>
	{/if}
  </ion-menu>
  
  <style>
	ion-item {
	  cursor: pointer;
	}
  
	.menuhide {
	  display: none;
	}
  </style>
  