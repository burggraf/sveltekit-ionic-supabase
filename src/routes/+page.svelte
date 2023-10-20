<script lang="ts">
	import { IonPage } from 'ionic-svelte';
</script>
<IonPage>
<ion-header translucent={true}>
	<ion-toolbar>
		<ion-buttons slot="start">
			<ion-menu-button />
		</ion-buttons>
		<ion-title>Main</ion-title>
	</ion-toolbar>
</ion-header>
<ion-content class="ion-padding">
	<h1>Main Page Content</h1>
</ion-content>
</IonPage>