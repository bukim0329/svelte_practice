<script>
	import { goto } from '$app/navigation';
	import { score, quizList } from '$lib/store.js';

	let isRunning = false;
	let list = $quizList;
	let idx = -1;
	let current = null;
	let bonus = 100;
	$score = 0;
	let timer;
	let kolor = {
		red: '빨강',
		orange: '주황',
		yellow: '노랑',
		purple: '보라',
		green: '초록',
		black: '검정',
		blue: '파랑',
		brown: '갈색',
		gray: '회색',
		pink: '분홍'
	};
	function start() {
		list.sort(() => Math.random() - 0.5);
		alert('준비됐나요?');
		$score = 0;
		next();
		isRunning = true;
	}

	function next() {
		idx = idx + 1;
		if (idx === list.length) {
			stop();
		} else {
			current = list[idx];
			current.choice.sort(() => Math.random() - 0.5);
			current = current;
			resetProgress();
		}
	}

	function resetProgress() {
		clearInterval(timer);
		bonus = 100;
		timer = setInterval(() => {
			bonus = bonus - 1;
			if (bonus === 0) {
				clearInterval(timer);
			}
		}, 40);
	}

	function stop() {
		idx = -1;
		clearInterval(timer);
		current = null;
		isRunning = false;
		alert('수고하였어요. 총 점수는' + $score + '점 입니다.');
		goto('/save');
	}

	function goHome() {
		goto('/');
	}
</script>

<svelte:head>
	<title>게임 하기</title>
</svelte:head>

{#if !isRunning}
	<h1 style="text-align:center">게임하는 방법</h1>
	<p>
		<b>1. 문제로 주어진 텍스트에 배경색이 [없을] 경우</b><br />
		-> 문제의 텍스트와 선택지의 글자색이 같은 것을 고릅니다<br /><br />

		<b>2. 문제로 주어진 텍스트에 배경색이 [있을] 경우</b><br />
		=> 문제의 배경색과 선택지의 텍스트가 같은 것을 고릅니다<br /><br />

		tip: 빨리 맞출수록 점수가 많아요!! 늦게 맞춰도 점수는 있으니 진행바에 쫄지 마세요.<br /><br />
		준비되었나요? 이제 시작 버튼을 누르고 도전!!
	</p>
{:else}
	<p>총 점수: {$score}</p>
	<div style="width:100%">
		<progress style="width:100%" value={bonus} max="100" />
	</div>
	<p />
	<p>{idx + 1} 번 문제!</p>

	{#if idx % 2 == 0}
		<div style="color:{current.question.color};background-color:white;" class="question">
			{kolor[current.question.text]}
		</div>
		{#each current.choice as item}
			<button
				style="color:{item.color};background-color:white;"
				class="choice"
				on:click={() => {
					$score = $score + (current.question.text == item.color ? 10 + bonus : 0);
					next();
				}}>{kolor[item.text]}</button
			>
		{/each}
	{:else}
		<div style="color:white;background-color:{current.question.color};" class="question">
			{kolor[current.question.text]}
		</div>
		<p />
		{#each current.choice as item}
			<button
				style="color:white;background-color:{item.color};"
				class="choice"
				on:click={() => {
					$score = $score + (current.question.color == item.text ? 10 + bonus : 0);
					next();
				}}>{kolor[item.text]}</button
			>
		{/each}
	{/if}
{/if}

<p style="text-align:center;">
	{#if isRunning}
		<button style="width:45%;height:60px;font-weight:bold;font-size:30px" on:click={stop}
			>그만하기</button
		>
	{:else}
		<button style="width:45%;height:60px;font-weight:bold;font-size:30px" on:click={start}
			>시작</button
		>
	{/if}
	<button style="width:45%;height:60px;font-weight:bold;font-size:30px" on:click={goHome}
		>홈으로</button
	>
</p>

<style>
	.question {
		margin: 0 auto;
		text-align: center;
		text-shadow: 1px 1px 1px #000;
		font-weight: bold;
		font-size: 60px;
		width: 150px;
	}
	.choice {
		text-shadow: 1px 1px 1px #000;
		width: 50%;
		height: 150px;
		font-weight: bold;
		font-size: 50px;
	}
</style>
