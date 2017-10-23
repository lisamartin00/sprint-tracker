<template>
  <div>
    <div class="row">
      <div class="col">
        <div class="form-group">
          <label for="projectId">Project ID:</label>
          <input type="text" class="form-control" id="projectId" :value="projectID" v-model="projectID" @keyup="loadData" />
        </div>
      </div>
      <div class="col">
        <div class="form-group">
          <label for="filters">Labels (comma separated):</label>
          <input type="text" class="form-control" id="filters" :value="filter" v-model="filter" @keyup="loadData" />
        </div>
      </div>
    </div>
    <h1>{{ projectName }}</h1>
    <div class="row">
      <div class="col stories">
        <div class="card">
          <div class="card-block">
            <div class="d-flex flex-row">
              <i class="fa fa-book" aria-hidden="true"></i>
              <div class="card-text">
                <h4 class="card-title">Stories</h4>
                <h3>{{ storiesComplete }}/{{ storiesTotal }}</h3>
              </div>
            </div>       
            <div class="progress">
              <div class="progress-bar" role="progressbar" v-bind:style="{ width: storiesPercent + '%' }" :aria-valuenow="storiesPercent" aria-valuemin="0" :aria-valuemax="100"></div>
            </div>
          </div>
        </div>
      </div>
      <div class="col features">
        <div class="card">
          <div class="card-block">
            <div class="d-flex flex-row">
              <i class="fa fa-star" aria-hidden="true"></i>
              <div class="card-text">
                <h4 class="card-title">Features</h4>
                <h3>{{ featuresComplete }}/{{ featuresTotal }}</h3>
                <h5>{{ pointsComplete }}/{{ pointsTotal }} points</h5>
              </div>
            </div>
            <div class="progress">
              <div class="progress-bar" role="progressbar" v-bind:style="{ width: featuresPercent + '%' }" :aria-valuenow="featuresPercent" aria-valuemin="0" aria-valuemax="100"></div>
            </div>
          </div>
        </div>
      </div>
      <div class="col bugs">
        <div class="card">
          <div class="card-block">
             <div class="d-flex flex-row">
              <i class="fa fa-bug" aria-hidden="true"></i>
              <div class="card-text">
                <h4 class="card-title">Bugs</h4>
                <h3>{{ bugsComplete }}/{{ bugsTotal }}</h3>
                <h5>{{ zenDeskComplete }}/{{ zenDeskTotal }} Zendesk</h5>
              </div>
            </div>
            <div class="progress">
              <div class="progress-bar" role="progressbar" v-bind:style="{ width: bugsPercent + '%' }" :aria-valuenow="bugsPercent" aria-valuemin="0" aria-valuemax="100"></div>
            </div>
          </div>
        </div>
      </div>
      <div class="col chores">
        <div class="card">
          <div class="card-block">
            <div class="d-flex flex-row">
              <i class="fa fa-cog" aria-hidden="true"></i>
              <div class="card-text">
                <h4 class="card-title">Chores</h4>
                <h3>{{ choresComplete }}/{{ choresTotal }}</h3>
              </div>
            </div>
            <div class="progress">
              <div class="progress-bar" role="progressbar" v-bind:style="{ width: choresPercent + '%' }" :aria-valuenow="choresPercent" aria-valuemin="0" aria-valuemax="100"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <table class="table">
          <thead>
            <tr>
              <th>ID</th>
              <th>Type</th>
              <th>Name</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(story, index) in stories" :key='index' :class="story.story_type" v-if="story.story_type !== 'release'">
              <td>{{ story.id }}</td>
              <td>{{ story.story_type }}</td>
              <td><a :href="story.url" target="_blank">{{ story.name }}</a></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
const PIVOTAL_TOKEN = 'a953c3376f19384b5742e71e69509307';
export default {
  name: 'Home',
  data: () => ({
    bugsComplete: 0,
    bugsTotal: 0,
    bugsPercent: 0,
    choresComplete: 0,
    choresTotal: 0,
    choresPercent: 0,
    featuresComplete: 0,
    featuresTotal: 0,
    featuresPercent: 0,
    pointsComplete: 0,
    pointsTotal: 0,
    storiesComplete: 0,
    storiesTotal: 0,
    storiesPercent: 0,
    zenDeskComplete: 0,
    zenDeskTotal: 0,
    stories: [],
    projectID: '1780535',
    projectName: '',
    filter: 'sprint 20, web',
    urlBase: 'https://www.pivotaltracker.com/services/v5/',
  }),
  methods: {
    aggregateData(data) {
      let storiesTotalCount = 0;
      let storiesCompleteCount = 0;
      let bugCompleteCount = 0;
      let bugTotalCount = 0;
      let choreCompleteCount = 0;
      let choreTotalCount = 0;
      let featureCompleteCount = 0;
      let featureTotalCount = 0;
      let pointsCompleteCount = 0;
      let pointsTotalCount = 0;
      let zenDeskCompleteCount = 0;
      let zenDeskTotalCount = 0;


      // TODO:  YUCK.  This is silly.  Is it better to make more API requests or loop
      // over everything we have to get what we want?  Is there a better way to tally
      // all of this?
      data.forEach((element) => {
        switch (element.story_type) {
          case 'bug':
            storiesTotalCount += 1;
            bugTotalCount += 1;
            if (element.external_id) {
              zenDeskTotalCount += 1;
            }
            if (element.current_state === 'accepted') {
              storiesCompleteCount += 1;
              bugCompleteCount += 1;

              if (element.external_id) {
                zenDeskCompleteCount += 1;
              }
            }
            break;
          case 'chore':
            storiesTotalCount += 1;
            choreTotalCount += 1;
            if (element.current_state === 'accepted') {
              storiesCompleteCount += 1;
              choreCompleteCount += 1;
            }
            break;
          case 'feature':
            storiesTotalCount += 1;
            featureTotalCount += 1;
            pointsTotalCount += element.estimate;
            if (element.current_state === 'accepted') {
              storiesCompleteCount += 1;
              featureCompleteCount += 1;
              pointsCompleteCount += element.estimate;
            }
            break;
          default:
            // Do nothing for other story types
            // (Release bars, for example)
            break;
        }
      });

      this.storiesComplete = storiesCompleteCount;
      this.storiesTotal = storiesTotalCount;
      this.featuresComplete = featureCompleteCount;
      this.featuresTotal = featureTotalCount;
      this.bugsComplete = bugCompleteCount;
      this.bugsTotal = bugTotalCount;
      this.choresComplete = choreCompleteCount;
      this.choresTotal = choreTotalCount;
      this.pointsTotal = pointsTotalCount;
      this.pointsComplete = pointsCompleteCount;
      this.storiesPercent = this.calculatePercent(this.storiesComplete, this.storiesTotal);
      this.featuresPercent = this.calculatePercent(this.featuresComplete, this.featuresTotal);
      this.bugsPercent = this.calculatePercent(this.bugsComplete, this.bugsTotal);
      this.choresPercent = this.calculatePercent(this.choresComplete, this.choresTotal);
      this.zenDeskComplete = zenDeskCompleteCount;
      this.zenDeskTotal = zenDeskTotalCount;
    },
    buildFilterString() {
      const labels = this.filter.split(',');
      let filterString = '';
      labels.forEach((label) => {
        const trimmedLabel = label.trim();
        filterString += `label:"${trimmedLabel}" `;
      });
      return filterString;
    },
    calculatePercent(complete, total) {
      return total === 0 ? 0 : Math.round(100 * (complete / total));
    },
    loadData() {
      this.loadProjectName();
      this.loadStories();
    },
    loadProjectName() {
      this.$http.get(`${this.urlBase}/projects/${this.projectID}`, { headers: { 'X-TrackerToken': PIVOTAL_TOKEN } })
      .then((response) => {
        this.projectName = response.body.name;
      });
    },
    loadStories() {
      const filterString = this.buildFilterString();
      this.$http.get(`${this.urlBase}projects/${this.projectID}/stories?filter=${filterString}`, { headers: { 'X-TrackerToken': PIVOTAL_TOKEN } })
      .then((response) => {
        this.stories = response.body;
        this.aggregateData(response.body);
      });
    },
  },
  mounted() {
    this.loadData();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h1 {
  font-weight: normal;
  margin: 20px 0;
}

.card {
  height: 200px;
  color: #fff;

  i {
    font-size: 56px;
    margin-right: 20px;
  }

  .progress {
    margin-top: auto;
  }

  .card-text {
    height: 130px;

    .card-title {
      margin-bottom: 5px;
    }

    h2 {
      margin-top: 2px;
      font-size: 28px;
    }

    h3 {
      font-size: 48px;
    }

    h5 {
      font-size: 18px;
    }
  }
}

$stories-color: #67c2ef;
$bugs-color: #ff5555;
$chores-color: #fabb3e;;
$features-color: #79c447;

.stories .card {
  background-color: $stories-color;

  .progress-bar {
    background-color: darken($stories-color, 20%);
  }
}

.bugs .card {
  background-color: $bugs-color;

  .progress-bar {
    background-color: darken($bugs-color, 20%);
  }
}

.chores .card {
  background-color: $chores-color;

  .progress-bar {
    background-color: darken($chores-color, 20%);
  }
}

.features .card {
  background-color: $features-color;

  .progress-bar {
    background-color: darken($features-color, 20%);
  }
}

.table {
  margin-top: 40px;
}


a {
  color: darken($stories-color, 20%);
}
</style>
