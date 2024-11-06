<script lang="ts">
	function getCurrentTimeFormatted(): string {
		const options: Intl.DateTimeFormatOptions = {
			month: 'short',
			day: 'numeric',
			hour: 'numeric',
			minute: 'numeric',
			hour12: true
		};
		const now = new Date();
		return now.toLocaleString('en-US', options);
	}

	let currentTime: string = getCurrentTimeFormatted();

	// Update the time every minute
	setInterval(() => {
		currentTime = getCurrentTimeFormatted();
	}, 60000);
</script>

<button class="btn btn-ghost btn-sm">
	{currentTime}
</button>
