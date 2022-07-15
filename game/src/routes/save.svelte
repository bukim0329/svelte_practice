<script>
	import { goto } from '$app/navigation';
	import { score, scoreList } from '$lib/store.js';

	let name = '--';

	let bodyData;
	let disabled = false;
	let message = '이름을 입력하고 저장 버튼을 누르세요.';

	$: bodyData = { score: $score, name: name };

	async function saveScore() {
		disabled = true;

		message = '점수 저장 중...';

		let rtn = await fetch('/api/score', {
			method: 'PUT',
			body: JSON.stringify(bodyData)
		});

		if (rtn.status == 200) {
			alert('저장되었습니다.');
		}

		goto('/');
	}
</script>

<svelte:head>
	<title>점수 저장하기</title>
</svelte:head>

<h1 style="text-align:center;">점수 저장하기</h1>

<h4>{message}</h4>

<p>획득점수: {$score}</p>

<p>이름: <input type="text" bind:value={name} {disabled} /></p>

<button on:click={saveScore} {disabled}> 저장 </button>
