<template>
	<header class="head"></header>
	<aside class="filter" v-if="selectedTags.length >= 1">
		<div class="filter-tags">
			<span v-for="(tag, index) in selectedTags" :key="index">
				<p class="tag tag-addon">{{ tag }}</p>
				<a class="tag-remove" href="#" @click.prevent="() => removeSelectedTag(tag)">
					<img src="../images/icon-remove.svg" alt="Remove" />
				</a>
			</span>
		</div>
		<a class="filter-clear" href="#" @click="clearSelectedTags()">Clear</a>
	</aside>
	<main :class="{ 'filter-active': selectedTags.length >= 1 }">
		<article
			class="job"
			:class="{ 'job-featured': job.featured }"
			v-for="job in filteredJobs.length !== 0 ? filteredJobs : jobs"
			:key="job.id"
		>
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
					<a class="tag" href="#" @click.prevent="() => addTagToFilter(job.role, 'role')">{{ job.role }}</a>
					<a class="tag" href="#" @click.prevent="() => addTagToFilter(job.level, 'level')">{{
						job.level
					}}</a>
					<a
						class="tag"
						href="#"
						@click.prevent="() => addTagToFilter(language, 'language')"
						v-for="(language, index) in job.languages"
						:key="index"
					>
						{{ language }}
					</a>
					<a
						class="tag"
						href="#"
						@click.prevent="() => addTagToFilter(tool, 'tool')"
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
	import { difference } from 'lodash';

	export default {
		data() {
			return {
				jobs: data,
				selectedTags: [],
				role: [],
				level: [],
				languages: [],
				tools: [],
				filteredJobs: []
			};
		},
		methods: {
			/* Tag adding functions */
			addTagToFilter(item, type) {
				if (!this.role.find((tag) => tag === item) && type === 'role') {
					this.role.push(item);
				} else if (!this.level.find((tag) => tag === item) && type === 'level') {
					this.level.push(item);
				} else if (!this.languages.find((tag) => tag === item) && type === 'language') {
					this.languages.push(item);
				} else if (!this.tools.find((tag) => tag === item) && type === 'tool') {
					this.tools.push(item);
				}
				this.concatArray();

				this.filtering();
			},

			/* Call the remove tags from specific category and refresh filtered jobs functions
			and check if there is any tag selected to clear filtered jobs if not */
			removeSelectedTag(e) {
				this.removeRole(e);
				this.removeLevel(e);
				this.removeLanguages(e);
				this.removeTools(e);
				this.selectedTags = this.selectedTags.filter((tag) => tag !== e);

				this.filtering();

				if (this.selectedTags.length === 0) {
					this.clearFilteredJobs();
				}
			},

			/* Remove tags from specific category and refresh filtered jobs functions */
			removeRole(e) {
				if (this.role.includes(e)) {
					this.role = this.role.filter((tag) => tag !== e);
				}
			},
			removeLevel(e) {
				if (this.level.includes(e)) {
					this.level = this.level.filter((tag) => tag !== e);
				}
			},
			removeLanguages(e) {
				if (this.languages.includes(e)) {
					this.languages = this.languages.filter((tag) => tag !== e);
				}
			},
			removeTools(e) {
				if (this.tools.includes(e)) {
					this.tools = this.tools.filter((tag) => tag !== e);
				}
			},

			/* Remove all filters */
			clearSelectedTags() {
				this.role = [];
				this.level = [];
				this.languages = [];
				this.tools = [];
				this.selectedTags = [];
				this.clearFilteredJobs();
			},

			/* Clear all filtered jobs function */
			clearFilteredJobs() {
				this.filteredJobs = [];
			},

			/* Group all selected tags from every categorie to one array */
			concatArray() {
				this.selectedTags = this.selectedTags
					.concat(difference(this.role, this.selectedTags))
					.concat(difference(this.level, this.selectedTags))
					.concat(difference(this.languages, this.selectedTags))
					.concat(difference(this.tools, this.selectedTags));
			},

			/* Filtering conditions */
			filtering() {
				if (this.role.length !== 0 && this.level.length === 0) {
					/*************************************/
					/* If there isn't any role but level */
					/*************************************/

					/* General */
					this.filteredJobs = this.jobs.filter((job) => job.role == this.role);

					/* Combining with languages and/or tools */
					if (this.languages.length !== 0 && this.tools.length === 0) {
						this.filteredJobs = this.filteredJobs.filter((job) =>
							job.languages.some((i) => this.languages.indexOf(i) !== -1)
						);
					} else if (this.languages.length === 0 && this.tools.length !== 0) {
						this.filteredJobs = this.filteredJobs.filter((job) =>
							job.tools.some((i) => this.tools.indexOf(i) !== -1)
						);
					} else if (this.languages.length !== 0 && this.tools.length !== 0) {
						this.filteredJobs = this.filteredJobs.filter(
							(job) =>
								job.languages.some((i) => this.languages.indexOf(i) !== -1) && job.role == this.role
						);
						this.filteredJobs = this.filteredJobs.concat(
							difference(
								this.jobs.filter(
									(job) =>
										job.tools.some((i) => this.tools.indexOf(i) !== -1) && job.role == this.role
								),
								this.filteredJobs
							)
						);
					}
				} else if (this.role.length === 0 && this.level.length !== 0) {
					/*************************************/
					/* If there isn't any level but role */
					/*************************************/

					/* General */
					this.filteredJobs = this.jobs.filter((job) => job.level == this.level);

					/* Combining with languages and/or tools */
					if (this.languages.length !== 0 && this.tools.length === 0) {
						this.filteredJobs = this.filteredJobs.filter((job) =>
							job.languages.some((i) => this.languages.indexOf(i) !== -1)
						);
					} else if (this.languages.length === 0 && this.tools.length !== 0) {
						this.filteredJobs = this.filteredJobs.filter((job) =>
							job.tools.some((i) => this.tools.indexOf(i) !== -1)
						);
					} else if (this.languages.length !== 0 && this.tools.length !== 0) {
						this.filteredJobs = this.filteredJobs.filter(
							(job) =>
								job.languages.some((i) => this.languages.indexOf(i) !== -1) && job.level == this.level
						);
						this.filteredJobs = this.filteredJobs.concat(
							difference(
								this.jobs.filter(
									(job) =>
										job.tools.some((i) => this.tools.indexOf(i) !== -1) && job.level == this.level
								),
								this.filteredJobs
							)
						);
					}
				} else if (this.role.length !== 0 && this.level.length !== 0) {
					/************************************/
					/* If there isn't any level or role */
					/************************************/

					/* General */
					this.filteredJobs = this.jobs.filter((job) => job.role == this.role);
					this.filteredJobs = this.filteredJobs.filter((job) => job.level == this.level);

					/* Combining with languages and/or tools */
					if (this.languages.length !== 0 && this.tools.length === 0) {
						this.filteredJobs = this.filteredJobs.filter((job) =>
							job.languages.some((i) => this.languages.indexOf(i) !== -1)
						);
					} else if (this.languages.length === 0 && this.tools.length !== 0) {
						this.filteredJobs = this.filteredJobs.filter((job) =>
							job.tools.some((i) => this.tools.indexOf(i) !== -1)
						);
					} else if (this.languages.length !== 0 && this.tools.length !== 0) {
						this.filteredJobs = this.filteredJobs.concat(
							difference(
								this.filteredJobs.filter((job) =>
									job.languages.some((i) => this.languages.indexOf(i) !== -1)
								),
								this.filteredJobs
							)
						);
						this.filteredJobs = this.filteredJobs.concat(
							difference(
								this.filteredJobs.filter((job) => job.tools.some((i) => this.tools.indexOf(i) !== -1)),
								this.filteredJobs
							)
						);
					}
				} else if (this.role.length === 0 && this.level.length === 0) {
					/*********************************/
					/* If there is any level or role */
					/*********************************/

					/* General */
					this.filteredJobs = this.jobs.filter((job) =>
						job.languages.some((i) => this.languages.indexOf(i) !== -1)
					);
					this.filteredJobs = this.filteredJobs.concat(
						difference(
							this.jobs.filter((job) => job.tools.some((i) => this.tools.indexOf(i) !== -1)),
							this.filteredJobs
						)
					);
				}

				/* Sort filtered jobs with id */
				this.filteredJobs.sort((a, b) => {
					return a.id - b.id;
				});
			}
		}
	};
</script>
