<template>
	<div class="uk-container">
		<div class="uk-margin-small-top uk-text-muted uk-text-right">サイト作成者: <a class="uk-link-muted" href="https://twitter.com/JADENgygo">@JADENgygo</a></div>
		<div class="uk-text-lead uk-text-center uk-margin-top">ガチャ告知画像<span class="title-break">ジェネレーター</span></div>
		<div uk-grid class="uk-grid-small uk-margin-top" v-if="!loaded">
			<div class="uk-width-1-5"></div>
			<div class="uk-width-3-5"><progress class="uk-progress" v-bind:value="progress" max="100"></progress></div>
			<div class="uk-width-1-5"></div>
		</div>
		<div v-bind:style="contentStyle" id="content">
			<div class="uk-form-stacked">
				<div class="uk-form-label uk-margin-top">背景画像</div>
				<div class="uk-form-controls">
					<div uk-grid class="uk-grid-small uk-child-width-1-2 uk-child-width-1-3@s">
						<div><img v-bind:src="backgroundImagePath" id="background-image"></div>
					</div>
				</div>
				<ul uk-accordion>
					<li>
						<a class="uk-accordion-title uk-text-small" href="#">背景画像プリセット</a>
						<div class="uk-accordion-content">
							<div uk-grid class="uk-grid-small uk-child-width-1-2 uk-child-width-1-3@s uk-child-width-1-4@m">
								<div v-for="i in 4" class="uk-text-center">
									<label>
										<div class="uk-form-controls">
											<div><input type="radio" class="uk-radio" v-bind:value="i - 1" v-model="presetImageIndex" v-on:click="clearSelectFile(i - 1)" v-bind:checked="i === 1"> {{ presetNames[i - 1] }}</div>
											<img v-bind:src="presetImagePaths[i - 1]">
										</div>
									</label>
								</div>
							</div>
						</div>
					</li>
				</ul>
				<div uk-form-custom class="uk-form-controls">
					<input type="file" v-on:change="selectBackgroundImage($event)" id="background-image-select" accept=".jpg,.jpeg,.png,.webp">
					<button class="uk-button uk-button-default uk-button-small">背景画像を選択</button>
				</div>
				<div uk-alert class="uk-alert-primary">画像は推奨サイズ(900×500)にトリミングされます</div>
				<label class="uk-form-label" for="loot-box-type">ガチャの種類</label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-medium" id="loot-box-type" type="text" v-model="lootBoxType" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="star">星</label>
				<select class="uk-select uk-form-small uk-form-width-xsmall" id="star" v-model="star" v-on:change="drawImage()">
					<option v-for="i in 5" v-bind:value="i">{{ '星' + i }}</option>
				</select>
				<label class="uk-form-label uk-margin-top" for="character-name">キャラ名</label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-medium" id="character-name" type="text" v-model="characterName" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="character-style">スタイル <span class="uk-text-muted">※キャラ名(スタイル)</span></label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-medium" id="character-style" type="text" v-model="characterStyle" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="cv">CV</label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-medium" id="cv" type="text" v-model="cv" v-on:input="drawImage()">
				</div>
				<div class="uk-form-label uk-margin-top">ポジション</div>
				<div class="uk-form-controls">
					<label class="uk-margin-small-right"><input class="uk-radio" type="radio" value="0" v-model="position" v-on:change="drawImage()"> 前衛</label>
					<label class="uk-margin-small-right"><input class="uk-radio" type="radio" value="1" v-model="position" v-on:change="drawImage()"> 中衛</label>
					<label><input class="uk-radio" type="radio" value="2" v-model="position" v-on:change="drawImage()"> 後衛</label>
				</div>
				<label class="uk-form-label uk-margin-top" for="hp">HP</label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-small" id="hp" type="number" v-model="hp" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="attack">物理攻撃力</label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-small" id="attack" type="number" v-model="attack" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="magic-attack">魔法攻撃力</label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-small" id="magic-attack" type="number" v-model="magicAttack" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="defense">物理防御力</label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-small" id="defense" type="number" v-model="defense" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="magic-defense">魔法防御力</label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-small" id="magic-defense" type="number" v-model="magicDefence" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="rank">ランク <span class="uk-text-muted">※RANK?/Lv?/装備強化MAXのステータスです。</span></label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-xsmall" id="rank" type="number" v-model="rank" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="level">レベル <span class="uk-text-muted">※RANK?/Lv?/装備強化MAXのステータスです。</span></label>
				<div class="uk-form-controls">
					<input class="uk-input uk-form-small uk-form-width-small" id="level" type="number" v-model="level" v-on:input="drawImage()">
				</div>
				<label class="uk-form-label uk-margin-top" for="description">キャラ説明</label>
				<div class="uk-form-controls">
					<textarea class="uk-textarea" rows="3" id="description" v-model="description" v-on:input="drawImage()"></textarea>
				</div>
				<label class="uk-form-label uk-margin-top" for="quote">セリフ</label>
				<div class="uk-form-controls">
					<textarea class="uk-textarea" rows="4" id="quote" v-model="quote" v-on:input="drawImage()"></textarea>
				</div>
				<label class="uk-form-label uk-margin-top" for="copyright">コピーライト <span class="uk-text-warning">(実在する人物・団体・企業などのそれと誤解され得る記述は推奨しません)</span></label>
				<div class="uk-form-controls">
					<textarea class="uk-textarea" rows="2" id="copyright" v-model="copyright" v-on:input="drawImage()"></textarea>
				</div>
				<label class="uk-form-label uk-margin-top" for="supplement">補足</label>
				<div class="uk-form-controls">
					<textarea class="uk-textarea" rows="2" id="supplement" v-model="supplement" v-on:input="drawImage()"></textarea>
				</div>
				<label class="uk-form-label uk-margin-top" for="ribbon-color">リボンの色</label>
				<div class="uk-form-controls">
					<input type="color" v-model="ribbonColor" id="ribbon-color" v-on:input="drawImage()" class="uk-margin-right">
					<button class="uk-button uk-button-default uk-button-small" v-on:click="resetRibbonColor()">リセット</button>
				</div>
				<label class="uk-form-label uk-margin-top" for="status-and-description-color">ステータスとキャラ説明の色</label>
				<div class="uk-form-controls">
					<input type="color" v-model="statusAndDescriptionColor" id="status-and-description-color" v-on:input="drawImage()" class="uk-margin-right">
					<button class="uk-button uk-button-default uk-button-small" v-on:click="resetStatusAndDescriptionColor()">リセット</button>
				</div>
				<label class="uk-form-label uk-margin-top" for="quote-color">セリフの色</label>
				<div class="uk-form-controls">
					<input type="color" v-model="quoteColor" id="quote-color" v-on:input="drawImage()" class="uk-margin-right">
					<button class="uk-button uk-button-default uk-button-small" v-on:click="resetQuoteColor()">リセット</button>
				</div>
				<button class="uk-button uk-button-default uk-button-small uk-margin-top" v-on:click="saveImage()">画像を保存</button>
				<div uk-alert class="uk-alert-primary">ボタンが動作しない時はプレビューを保存するか別ブラウザを使用してください</div>
				<div class="uk-form-label">プレビュー</div>
				<div class="uk-form-controls">
					<canvas id="canvas" width="900" height="500" v-bind:style="previewStyle" class="uk-margin-small-bottom"></canvas>
				</div>
			</div>
		</div>
		<div class="resource">
			<img v-bind:src="starImagePath" id="star-image">
			<img v-for="i in 3" v-bind:src="positionImagePaths[i - 1]" v-bind:id="'position-image' + (i - 1)">
			<canvas id="work-canvas"></canvas>
		</div>
	</div>
</template>
<script lang="ts">
import Vue from 'vue';
import Component from 'vue-class-component';
import WebFont from 'webfontloader';
import UIkit from 'uikit';

type DrawType = 'fill' | 'stroke';
type UIkitElement = object | HTMLElement | string;

@Component
export default class Host extends Vue {
	loaded: boolean = false;
	contentStyle: {[key: string]: string} = {display: 'none'};
	intervalId: number = 0;
	progress: number = 0;
	previewStyle: {[key: string]: string} = {};
	backgroundImagePath: string = require('./img/aqua0.webp');
	presetImageIndex: number = 0;
	presetNames: string[] = ['アクア1', 'アクア2', 'めぐみん', 'ダクネス'];
	presetImagePaths: string[] = [require('./img/aqua0.webp'),require('./img/aqua1.webp'), require('./img/megumin.webp'),require('./img/darkness.webp')];
	starImagePath: string = require('./img/star.webp');
	positionImagePaths: string[] = [require('./img/front.webp'), require('./img/center.webp'), require('./img/back.webp')];
	lootBoxType: string = '新キャラ登場！';
	star: number = 3;
	characterName: string = 'アクア';
	characterStyle: string = '';
	cv: string = '雨宮天';
	position: number = 1;
	hp: number = 60000;
	attack: number = 16000;
	magicAttack: number = 16000;
	defense: number = 1000;
	magicDefence: number = 800;
	rank: number = 20;
	level: number = 200;
	description: string = '【魔法】中衛で、神の力を使う、水を司る女神様。\n女神の怒りと悲しみを乗せた必殺の拳と、女神の\n愛と悲しみの鎮魂歌で、敵を浄化する。';
	quote: string = '「ゴッドレクイエムとは\n　　女神の愛と\n　　　悲しみの鎮魂歌！\n　　　　　　　相手は死ぬ！」';
	copyright: string = 'Fake Copyright Axis Cult.';
	supplement: string = '\n対象のガチャの内容や開催期間は、予告なく変更する可能性があります。';
	ribbonColor: string = '#0064FF';
	statusAndDescriptionColor: string = '#0064FF';
	quoteColor: string = '#0064FF';

	mounted(): void {
		WebFont.load({
			custom: {
				families: ['Kosugi Maru', 'Kosugi'],
			},
			timeout: 300000,
			loading: () => {
				this.intervalId = setInterval(() => {
					if (this.progress + 10 < 100) {
						this.progress += 10
					}
					else {
						this.progress = 99;
					}
				}, 2000);
			},
			active: () => {
				clearInterval(this.intervalId);
				this.progress = 100;
				setTimeout(() => {
					this.loaded = true;
					this.$set(this.contentStyle, 'display', 'block');
					UIkit.scrollspy(document.getElementById('content') as UIkitElement, {cls: 'uk-animation-fade'});

					const f = (style: string): void => {
						if (window.orientation === 0 && window.matchMedia(`(${style}-width:426px)`).matches) {
							this.$set(this.previewStyle, 'height', 'auto');
							this.$set(this.previewStyle, 'width', 'auto');
						}
						else {
							this.$set(this.previewStyle, 'height', 'auto');
							this.$set(this.previewStyle, 'width', '66%');
						}
					};
					f('max');
					window.addEventListener('resize', () => f('max'));
					// matchMediaは回転前の幅を判定しているらしいのでminとmaxが逆になる？
					window.addEventListener('orientationchange', () => f('min'));

					document.getElementById('background-image')?.addEventListener('load', () => {
						this.drawImage();
					});
					this.drawImage();
				}, 500);
			}
		});
	}

	saveImage(): void {
		const canvas = document.getElementById('canvas') as HTMLCanvasElement;
		const a = document.createElement('a');
		a.href = canvas.toDataURL('image/png');
		a.download = 'ガチャ告知.png';
		a.click();
	}

	clearSelectFile(index: number): void {
		(document.getElementById('background-image-select') as HTMLInputElement).value = '';
		this.backgroundImagePath = this.presetImagePaths[index];
	}

	selectBackgroundImage(event: any): void {
		// 一度ダイアログで画像を選択した後に、もう一度ダイアログを開いてキャンセルすると本関数が呼ばれるがevent.target.files[0]がundefinedになるため
		if (!event.target.files[0]) {
			return;
		}
		const reader = new FileReader();
		reader.readAsArrayBuffer(event.target.files[0]);
		reader.onload = (e: ProgressEvent<FileReader>): void => {
			const data = new Uint8Array(e.target?.result as ArrayBufferLike).subarray(0, 12);
			let header = '';
			for (let i = 0; i < data.length; ++i) {
				header += data[i].toString(16);
			}
			// png, jpg, webp
			if (/^89504e47/.test(header) || /^ffd8ff/.test(header) || /57454250$/.test(header)) {
				const r = new FileReader();
				r.readAsDataURL(event.target.files[0]);
				r.onload = () => {
					const image = new Image();
					image.src = r.result as string;
					image.onload = () => {
						const canvas = document.getElementById('work-canvas') as HTMLCanvasElement;
						canvas.width = 900;
						canvas.height = 500;
						let ratio;
						if (image.width === 900 && image.height === 500) {
							ratio = 1.0;
						}
						else if (1.8 <= 1.0 * image.width / image.height) {
							ratio = 1.0 * canvas.height / image.height;
						}
						else {
							ratio = 1.0 * canvas.width / image.width;
						}
						image.width *= ratio;
						image.height *= ratio;
						const context = canvas.getContext('2d') as CanvasRenderingContext2D;
						context.drawImage(image, 0, 0, image.width, image.height);
						this.presetImageIndex = -1;
						this.backgroundImagePath = canvas.toDataURL('image/webp', 1.0);
					};
				};
			}
			else {
				(document.getElementById('background-image-select') as HTMLInputElement).value = '';
				UIkit.notification({
					message: '対応していないファイル形式です',
					status: 'warning',
					pos: 'top-center',
					timeout: 5000
				});
			}
		};
	}

	drawImage(): void {
		const canvas = document.getElementById('canvas') as HTMLCanvasElement;
		const context = canvas.getContext('2d') as CanvasRenderingContext2D;
		context.clearRect(0, 0, canvas.width, canvas.height);
		
		// 共通設定
		context.textBaseline = 'top';
		context.lineJoin = 'round';
		context.lineCap = 'round';
		context.miterLimit = 1;

		// 背景
		const b = document.getElementById('background-image') as HTMLCanvasElement;
		context.drawImage(b, 0, 0);

		// リボンの影
		context.fillStyle = 'rgb(0, 0, 0, 0.3)';
		context.beginPath();
		context.moveTo(0, 20);
		context.lineTo(240, 20);
		context.lineTo(230, 45);
		context.lineTo(240, 70);
		context.lineTo(0, 70);
		context.fill();
		
		// リボン
		{
			const rgb = this.convertHexToRgb(this.ribbonColor);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
		}
		context.beginPath();
		context.moveTo(0, 15);
		context.lineTo(240, 15);
		context.lineTo(230, 40);
		context.lineTo(240, 65);
		context.lineTo(0, 65);
		context.fill();

		// リボンの柄
		context.save();
		context.beginPath();
		context.moveTo(0, 15);
		context.lineTo(240, 15);
		context.lineTo(230, 40);
		context.lineTo(240, 65);
		context.lineTo(0, 65);
		context.clip();
		{
			const rgb = this.convertHexToRgb(this.ribbonColor).map(e => 0 <= e - 10 ? e - 10 : 0);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
		}
		for (let i = 0; i < 9; ++i) {
			context.beginPath();
			context.moveTo(5 + i * 30, 15);
			context.lineTo(20 + i * 30, 30);
			context.lineTo(5 + i * 30, 45);
			context.lineTo(-10 + i * 30, 30);
			context.fill();
			context.beginPath();
			context.moveTo(5 + i * 30, 45);
			context.lineTo(20 + i * 30, 60);
			context.lineTo(5 + i * 30, 75);
			context.lineTo(-10 + i * 30, 60);
			context.fill();
		}
		context.restore();

		// リボンの線
		context.strokeStyle = 'white';
		context.lineWidth = 3;
		context.beginPath();
		context.moveTo(0, 21);
		context.lineTo(238, 21);
		context.moveTo(0, 59);
		context.lineTo(238, 59);
		context.stroke();

		// ガチャの種類
		context.lineWidth = 5;
		for (let i = 0; i < this.lootBoxType.length; ++i) {
			context.font = this.lootBoxType[i].match(/[!！♪]/) ? "italic bold 25px 'Kosugi Maru'" : "bold 25px 'Kosugi Maru'";
			const rgb = this.convertHexToRgb(this.ribbonColor).map(e => 0 <= e - 15 ? e - 15 : 0);
			context.strokeStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`;
			context.strokeText(this.lootBoxType[i], 20 + i * 25, 28);
			context.fillStyle = 'white';
			context.fillText(this.lootBoxType[i], 20 + i * 25, 28);
		}


		// 星
		for (let i = 1; i <= this.star; ++i) {
			const s = document.getElementById('star-image') as HTMLImageElement;
			context.drawImage(s, 20 + (i - 1) * 31, 75);
		}

		// 名前
		context.font = "bold 50px 'Kosugi'";
		context.strokeStyle = 'rgba(0, 0, 0, 0.3)';
		context.lineWidth = 5;
		context.strokeText(this.characterName, 20, 113);

		context.fillStyle = 'rgba(0, 0, 0, 0.3)';
		context.fillText(this.characterName, 20, 113);
		{
			context.beginPath();
			const strokeGradient = context.createLinearGradient(20, 110, 20, 160);
			strokeGradient.addColorStop(0.0, 'rgb(191, 147, 82)');
			strokeGradient.addColorStop(1.0, 'rgb(90, 56, 8)');
			context.strokeStyle = strokeGradient;
			context.lineWidth = 5;
			context.strokeText(this.characterName, 20, 110);

			const fillGradient = context.createLinearGradient(20, 110, 20, 160);
			fillGradient.addColorStop(0.0, 'rgb(255, 252, 225)');
			fillGradient.addColorStop(1.0, 'rgb(221, 184, 103)');
			context.fillStyle = fillGradient;
			context.fillText(this.characterName, 20, 110);
		}

		// キャラクタースタイル
		{
			const offset = context.measureText(this.characterName).width;
			context.font = "bold 35px 'Kosugi'";
			context.strokeStyle = 'rgba(0, 0, 0, 0.3)';
			context.lineWidth = 5;
			context.strokeText(this.characterStyle, 20 + offset, 125);

			context.fillStyle = 'rgba(0, 0, 0, 0.3)';
			context.fillText(this.characterStyle, 20 + offset, 125);

			context.beginPath();
			const strokeGradient = context.createLinearGradient(20 + offset, 122, 20 + offset, 157);
			strokeGradient.addColorStop(0.0, 'rgb(191, 147, 82)');
			strokeGradient.addColorStop(1.0, 'rgb(90, 56, 8)');
			context.strokeStyle = strokeGradient;
			context.lineWidth = 5;
			context.strokeText(this.characterStyle, 20 + offset, 122);

			const fillGradient = context.createLinearGradient(20 + offset, 122, 20 + offset, 157);
			fillGradient.addColorStop(0.0, 'rgb(255, 252, 225)');
			fillGradient.addColorStop(1.0, 'rgb(221, 184, 103)');
			context.fillStyle = fillGradient;
			context.fillText(this.characterStyle, 20 + offset, 122);
		}

		// CV
		context.font = "bold 25px 'Kosugi Maru'";
		{
			const gradient = context.createLinearGradient(20, 165, 20, 190);
			gradient.addColorStop(0.0, 'rgb(176, 138, 93)');
			gradient.addColorStop(1.0, 'rgb(90, 80, 53)');
			context.strokeStyle = gradient;
			context.lineWidth = 5;
			context.strokeText('CV:' + this.cv, 20, 165);

			context.fillStyle = 'white';
			context.fillText('CV:' + this.cv, 20, 165);
		}

		// 位置
		const p = document.getElementById('position-image' + this.position) as HTMLImageElement;
		context.drawImage(p, 20, 200);

		// HP
		context.font = "bold 20px 'Kosugi Maru'";
		context.strokeStyle = 'white';
		context.lineWidth = 5;
		context.strokeText('HP……' + this.hp, 20, 225);
		{
			const rgb = this.convertHexToRgb(this.statusAndDescriptionColor);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
		}
		context.fillText('HP……' + this.hp, 20, 225);

		// 物理攻撃力
		context.font = "bold 20px 'Kosugi Maru'";
		context.strokeStyle = 'white';
		context.lineWidth = 5;
		context.strokeText('物理攻撃力……' + this.attack, 20, 250);
		{
			const rgb = this.convertHexToRgb(this.statusAndDescriptionColor);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
		}
		context.fillText('物理攻撃力……' + this.attack, 20, 250);

		// 魔法攻撃力
		context.font = "bold 20px 'Kosugi Maru'";
		context.strokeStyle = 'white';
		context.lineWidth = 5;
		context.strokeText('魔法攻撃力……' + this.magicAttack, 20, 275);
		{
			const rgb = this.convertHexToRgb(this.statusAndDescriptionColor);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
		}
		context.fillText('魔法攻撃力……' + this.magicAttack, 20, 275);

		// 物理防御力
		context.font = "bold 20px 'Kosugi Maru'";
		context.strokeStyle = 'white';
		context.lineWidth = 5;
		context.strokeText('物理防御力……' + this.defense, 20, 300);
		{
			const rgb = this.convertHexToRgb(this.statusAndDescriptionColor);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
		}
		context.fillText('物理防御力……' + this.defense, 20, 300);

		// 魔法防御力
		context.font = "bold 20px 'Kosugi Maru'";
		context.strokeStyle = 'white';
		context.lineWidth = 5;
		context.strokeText('魔法防御力……' + this.magicDefence, 20, 325);
		{
			const rgb = this.convertHexToRgb(this.statusAndDescriptionColor);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
		}
		context.fillText('魔法防御力……' + this.magicDefence, 20, 325);

		// ステータス補足
		context.font = "bold 15px 'Kosugi Maru'";
		context.strokeStyle = 'white';
		context.lineWidth = 5;
		context.strokeText(`※RANK${this.rank}/Lv${this.level}/装備強化MAXのステータスです。`, 20, 350);
		{
			const rgb = this.convertHexToRgb(this.statusAndDescriptionColor);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
		}
		context.fillText(`※RANK${this.rank}/Lv${this.level}/装備強化MAXのステータスです。`, 20, 350);

		// キャラ説明
		context.font = "bold 20px 'Kosugi Maru'";
		{
			context.strokeStyle = 'white';
			context.lineWidth = 5;
			const xs = Array((this.description.match(/\n/g) || []).length + 1).fill(20);
			const ys = Array((this.description.match(/\n/g) || []).length + 1).fill(379).map((e, i) => e + i * 24);
			this.drawString(context, 'stroke', xs, ys, this.description);

			const rgb = this.convertHexToRgb(this.statusAndDescriptionColor);
			context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
			this.drawString(context, 'fill', xs, ys, this.description);
		}

		// 補足
		context.font = "bold 13px 'Kosugi Maru";
		{
			context.strokeStyle = 'black';
			context.lineWidth = 2;
			const xs = Array((this.supplement.match(/\n/g) || []).length + 1).fill(20);
			const ys = Array((this.supplement.match(/\n/g) || []).length + 1).fill(459).map((e, i) => e + i * 17);
			this.drawString(context, 'stroke', xs, ys, this.supplement);
			context.fillStyle = 'white';
			this.drawString(context, 'fill', xs, ys, this.supplement);
		}

		// コピーライト
		context.font = "bold 13px 'Kosugi Maru'";
		{
			context.strokeStyle = 'rgb(100, 100, 100)';
			context.lineWidth = 4;
			const xs = Array((this.copyright.match(/\n/g) || []).length + 1).fill(0).map((e, i) => canvas.width / 2 + (canvas.width / 2 - context.measureText(this.copyright.split('\n')[i]).width - 20));
			const ys = Array((this.copyright.match(/\n/g) || []).length + 1).fill(15).map((e, i) => e + i * 17);
			this.drawString(context, 'stroke', xs, ys, this.copyright);
			context.fillStyle = 'white';
			this.drawString(context, 'fill', xs, ys, this.copyright);
		}

		// セリフ
		context.lineWidth = 5;
		context.rotate(Math.PI / 50.0);
		{
			let x = 850;
			let x_ = 850;
			let y = 0;
			const xOffset = -35;
			const yOffset = 30;
			let start = 0;
			const rgb = this.convertHexToRgb(this.quoteColor);
			for (let i = 0; i < this.quote.length; ++i) {
				if (this.quote[i] === '\n') {
					x += xOffset;
					x_ -= xOffset;
					y = 0;
					start = 0;
					continue;
				}
				let xExtraOffset = 0;
				let yExtraOffset = 0;
				if (this.quote[i].match(/[、。,\.]/)) {
					xExtraOffset = Math.abs(xOffset) / 2;
					yExtraOffset = -Math.abs(yOffset) / 2;
				}
				if (this.quote[i].match(/[\'’]/)) {
					xExtraOffset = Math.abs(xOffset) / 2;
				}
				if (this.quote[i].match(/[a-zA-Z!\?]/)) {
					xExtraOffset = Math.abs(xOffset) / 5;
				}
				if (this.quote[i].match(/[っぁぃぅぇぉゃゅぉッァィゥェォャュョ]/)) {
					xExtraOffset = Math.abs(xOffset) / 10;
					yExtraOffset = -Math.abs(yOffset) / 7;
				}
				let font = "bold 30px 'Kosugi Maru'";
				if (this.quote[i].match(/[!！♪]/)) {
					font = 'italic ' + font;
				}
				context.font = font;
				if (this.quote[i].match(/[＝\=＿_￣ー\-―－～｜\|\[\]［］\{\}｛｝\(\)（）【】『』「」…]/)) {
					context.rotate(Math.PI / 2.0);
					context.strokeStyle = 'white';
					context.strokeText(this.quote[i], y + start * yOffset, x_ - 1730);
					context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
					context.fillText(this.quote[i], y + start * yOffset, x_ - 1730);
					context.rotate(-Math.PI / 2.0);
				}
				else {
					context.strokeStyle = 'white';
					context.strokeText(this.quote[i], x + xExtraOffset, y + start * yOffset + yExtraOffset);
					context.fillStyle = `rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`
					context.fillText(this.quote[i], x + xExtraOffset, y + start * yOffset + yExtraOffset);
				}
				start++;
			}
		}
		context.rotate(-Math.PI / 50.0);
	}

	drawString(context: CanvasRenderingContext2D, drawType: DrawType, xs: number[], ys: number[], text: string): void {
		let breakPos = 0;
		let row = 0;
		for (let i = 0; i < text.length; ++i) {
			if (text[i] === '\n') {
				if (drawType === 'stroke') {
					context.strokeText(text.substring(breakPos, i), xs[row], ys[row]);
				}
				else {
					context.fillText(text.substring(breakPos, i), xs[row], ys[row]);
				}
				breakPos = i + 1;
				row++;
			}
			if (i === text.length - 1) {
				if (drawType === 'stroke') {
					context.strokeText(text.substring(breakPos), xs[row], ys[row]);
				}
				else {
					context.fillText(text.substring(breakPos), xs[row], ys[row]);
				}
			}
		}
	}

	convertHexToRgb(hex: string): number[] {
		const c = [hex.substr(1, 2), hex.substr(3, 2), hex.substr(5, 2)];
		let rgb = [];
		for (let e of c) {
			let buf = 0;
			const weight = [16, 1];
			for (let i = 0; i < 2; ++i) {
				switch (e[i].toUpperCase()) {
					case 'A':
						buf += 10 * weight[i];
						break;
					case 'B':
						buf += 11 * weight[i];
						break;
					case 'C':
						buf += 12 * weight[i];
						break;
					case 'D':
						buf += 13 * weight[i];
						break;
					case 'E':
						buf += 14 * weight[i];
						break;
					case 'F':
						buf += 15 * weight[i];
						break;
					default:
						buf += parseInt(e[i]) * weight[i];
						break;
				}
			}
			rgb.push(buf);
		}
		return rgb;
	}

	resetRibbonColor(): void {
		this.ribbonColor = '#0064FF';
		this.drawImage();
	}

	resetStatusAndDescriptionColor(): void {
		this.statusAndDescriptionColor = '#0064FF';
		this.drawImage();
	}

	resetQuoteColor(): void {
		this.quoteColor = '#0064FF';
		this.drawImage();
	}
}
</script>
<style scoped>
@media screen and (max-width: 420px) {
	.title-break::before {
		content: '\A';
		white-space: pre;
	}
}

#footer {
	background: white;
	padding: 0 0 10px 0;
}

.resource {
	display: none;
}
</style>
