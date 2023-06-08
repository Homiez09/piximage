<script lang="ts">
	import { onMount } from 'svelte';

	let fileinput;
	let pixelSize = 6;
	let mainImage: Blob;

	const convertToPixel = (imagePath: string, pixelSize: number) => {
		const image = new Image();
		image.src = imagePath;
		image.alt = 'piximage';

		image.onload = () => {
			const canvas = document.getElementById('canvas') as HTMLCanvasElement;
			const ctx = canvas.getContext('2d') as CanvasRenderingContext2D;

			const newWidth = Math.floor(image.width / pixelSize);
			const newHeight = Math.floor(image.height / pixelSize);

			canvas.width = newWidth;
			canvas.height = newHeight;

			ctx.drawImage(image, 0, 0, newWidth, newHeight);
		};
	};

	const onFileSelected = (e: any) => {
		mainImage = e.target!.files[0];
		let reader = new FileReader();
		reader.readAsDataURL(mainImage);
		reader.onload = () => {
			const imagePath = reader.result as string;
			convertToPixel(imagePath, pixelSize);
		};
	};

	onMount(() => {
		const downloadButton = document.getElementById('downloadButton') as Element;
		const reloadButton = document.getElementById('reloadButton') as Element;

		downloadButton.addEventListener('click', () => {
			const canvas = document.getElementById('canvas') as HTMLCanvasElement;
			const dataURL = canvas.toDataURL('image/png');
            console.log(dataURL)
			const link = document.createElement('a');
			const chars = '0123456789abcdefghijklmnopqrstuvwxyz!@#$%^&*()ABCDEFGHIJKLMNOPQRSTUVWXYZ';
            let name = '';
			for (let i = 0; i <= 5; i++) {
				const randomNumber = Math.floor(Math.random() * chars.length);
				name += chars.substring(randomNumber, randomNumber + 1);
			}
			link.href = dataURL;
			link.download = 'piximage-' + name + '.png';
			link.click();
		});

		reloadButton.addEventListener('click', () => {
			let reader = new FileReader();
			reader.readAsDataURL(mainImage);
			reader.onload = () => {
				const imagePath = reader.result as string;
				convertToPixel(imagePath, pixelSize);
			};
		});
	});
</script>

<div class="flex flex-col min-h-screen items-center justify-center gap-3 py-9">
	<h1 class="text-4xl">PIXIMAGE</h1>
    <div class="flex w-full px-2 sm:px-40">
        <canvas id="canvas" class="w-full h-auto border-2 rounded-2xl" />
    </div>

	<input
		on:change={(e) => onFileSelected(e)}
		bind:this={fileinput}
		type="file"
		accept=".jpg, .png"
		id="imageInput"
		class="cursor-pointer text-sm text-slate-500
        file:cursor-pointer
        file:mr-4 file:py-2 file:px-4
        file:rounded-full file:border-0
        file:text-sm file:font-semibold
        file:bg-violet-50 file:text-violet-700
        hover:file:bg-violet-100"
	/>
	<input id="pixelSize" type="number" bind:value={pixelSize} min="1" max="10" />
	<input id="pixelSize" type="range" bind:value={pixelSize} min="1" max="10" />
	<div class="flex flex-row gap-2">
		<button id="reloadButton" class="btn btn-secondary">reload</button>
		<button id="downloadButton" class="btn btn-primary">download</button>
	</div>
</div>
