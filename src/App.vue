<template>
	<header class="head">
		<aside class="filter" v-if="selectedTags.length >= 1">
			<div class="filter-tags">
				<span v-for="(tag, index) in selectedTags" :key="index">
					<p class="tag tag-addon">{{ tag }}</p>
					<a class="tag-remove" href="#" @click.prevent="() => removeSelectedTag(tag)">
						<img src="../images/icon-remove.svg" alt="Remove" />
					</a>
				</span>
			</div>
			<a class="filter-clear" href="#" @click="clearSelectedTags">Clear</a>
		</aside>
	</header>
	<main>
		<article class="job" v-for="job in filteredJobs.length > 0 ? filteredJobs : jobs" :key="job.id">
			<img class="job-img" :src="job.logo" :alt="job.company" />
			<div class="job-content">
				<header class="job-main">
					<div class="job-head">
						<h3>{{ job.company }}</h3>
						<div class="pills" v-if="job.new || job.featured">
							<span v-if="job.new" class="pill pill-new">NEW!</span>
							<span v-if="job.featured" class="pill pill-featured">Featured</span>
						</div>
					</div>
					<a class="job-position" href="#">
						<h2>{{ job.position }}</h2>
					</a>
					<div class="job-details">
						<p>{{ job.postedAt }}<span>•</span></p>
						<p>{{ job.contract }}<span>•</span></p>
						<p>{{ job.location }}</p>
					</div>
				</header>
				<aside class="job-tags">
					<a class="tag" href="#" @click.prevent="() => addTagToFilter(job.role)">{{ job.role }}</a>
					<a class="tag" href="#" @click.prevent="() => addTagToFilter(job.level)">{{ job.level }}</a>
					<a
						class="tag"
						href="#"
						@click.prevent="() => addTagToFilter(language)"
						v-for="(language, index) in job.languages"
						:key="index"
					>
						{{ language }}
					</a>
					<a
						class="tag"
						href="#"
						@click.prevent="() => addTagToFilter(tool)"
						v-for="(tool, index) in job.tools"
						:key="index"
					>
						{{ tool }}
					</a>
				</aside>
			</div>
		</article>
	</main>
</template>

<script>
	import data from './assets/json/data.json';

	export default {
		data() {
			return {
				jobs: data,
				selectedTags: [],
				filteredJobs: []
			};
		},
		methods: {
			addTagToFilter(item) {
				if (!this.selectedTags.find((tag) => tag === item)) {
					this.selectedTags.push(item);
					this.filteredJobs = this.filterJobs;
				} else return;
			},
			removeSelectedTag(e) {
				this.selectedTags = this.selectedTags.filter((tag) => tag !== e);
				this.filteredJobs = this.filterJobs;
				if (this.selectedTags.length === 0) {
					this.clearFilteredJobs();
				}
			},
			clearSelectedTags() {
				this.selectedTags = [];
				this.clearFilteredJobs();
			},
			clearFilteredJobs() {
				this.filteredJobs = [];
			}
		},
		computed: {
			filterJobs() {
				return this.jobs.filter(
					(o) =>
						this.selectedTags.includes(o.role) ||
						this.selectedTags.includes(o.level) ||
						this.selectedTags.includes(...o.languages) ||
						this.selectedTags.includes(...o.tools)
				);
			}
		}

		/*
		TODO:
		- add responsive design for mobiles
		- rework jobs filtering so that it shows only jobs that have every tag that is selected
		*/
	};
</script>

<style></style>
