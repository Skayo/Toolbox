<script>
	import { writable } from 'svelte/store';
	import Tool from './Tool.svelte';

	let size = writable(200);
	let encoding = writable('UTF-8');
	let ecc = writable('L');
	let fgcolor = writable('#000');
	let bgcolor = writable('#fff');
	let margin = writable(10);
	let format = writable('png');

	const settings = [
		{
			type:        'number',
			name:        'Image Size (px)',
			placeholder: 'f.e. 200',
			bind:        size,
			min:         10,
			max:         1000,
		},
		{
			type:    'select',
			name:    'Encoding',
			options: [
				{ name: 'UTF-8', value: 'UTF-8' },
				{ name: 'ISO-8859-1', value: 'ISO-8859-1' },
			],
			bind:    encoding,
		},
		{
			type:    'select',
			name:    'Error Correction Level',
			options: [
				{ name: 'Low', value: 'L' },
				{ name: 'Middle', value: 'M' },
				{ name: 'Quartile', value: 'Q' },
				{ name: 'High', value: 'H' },
			],
			bind:    ecc,
		},
		{
			type:        'text',
			name:        'Foreground Color (Hexadecimal)',
			placeholder: 'f.e. #000',
			bind:        fgcolor,
		},
		{
			type:        'text',
			name:        'Background Color (Hexadecimal)',
			placeholder: 'f.e. #fff',
			bind:        bgcolor,
		},
		{
			type:        'number',
			name:        'Margin (px)',
			placeholder: 'f.e. 10',
			bind:        margin,
			min:         0,
			max:         50,
		},
		{
			type:    'select',
			name:    'File Format',
			options: [
				{ name: 'PNG', value: 'png' },
				{ name: 'JPEG', value: 'jpg' },
				{ name: 'GIF', value: 'gif' },
				{ name: 'WEBP', value: 'webp' },
			],
			bind:    format,
		},
	];

	let data = '';
	let timeout;
	let value;

	$: qrCodeUrl = 'https://mage.skayo.dev/qr' +
	               `/${$size}` +
	               `/${$bgcolor.replace('#', '')}` +
	               `/${$fgcolor.replace('#', '')}` +
	               `/${$margin}` +
	               `/${$ecc}` +
	               `/${$encoding}` +
	               `.${$format}` +
	               `?${data}`;

	function updateQrData() {
		clearTimeout(timeout);

		timeout = setTimeout(() => {
			data = value;
		}, 1000);
	}
</script>

<Tool title="QR-Code Generator" {settings} settingsInfo="http://goqr.me/api/doc/create-qr-code/">
	<div class="uk-grid-small" uk-grid>
		<div class="uk-width-expand@s uk-width-1-1">
			<textarea bind:value on:input={updateQrData} class="uk-textarea uk-margin-small-bottom" placeholder="Text, URL, etc..."></textarea>
			<a href="https://github.com/zxing/zxing/wiki/Barcode-Contents" target="_blank">List of QR-Code Content Standards</a>
		</div>

		{#if data !== ''}
			<div class="uk-width-1-4@s uk-width-1-1 uk-text-center">
				<img src={qrCodeUrl} class="uk-width-1-1 uk-margin-small-bottom" alt="QR-Code">
				<a href="{qrCodeUrl}" download="qrcode.png" target="_blank" class="uk-button uk-button-primary uk-button-small uk-width-1-1">Download</a>
			</div>
		{/if}
	</div>
</Tool>
