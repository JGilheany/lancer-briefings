<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Bug-Hunt",
          "status": "success"
        },
        {
          "slug": "002",
          "name": "Vigilant Gaze",
          "status": "start"
        },
        {
          "slug": "003",
          "name": "Stalwart Placeholder",
          "status": "start",
        },
      ],
      "pilots": [
        {
          "callsign": "Axel",
          "alias": "Jebidiah Diaz",
          "code": "61d141ca-e9fd-4612-94c1-54e867fa5587Diaz.Jebediah:61d141ca-e9fd-4612-94c1-54e867fa5587//NDL-C-BLACK-STATION",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Bertha"
        },
        {
          "callsign": "Eagle",
          "alias": "John War II",
          "code": "86879ffc-2891-44ed-8885-93bb4f62f871War.John:86879ffc-2891-44ed-8885-93bb4f62f871//NDL-C-BLOOD-ORCHID",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Bob Battle"
        },
        {
          "callsign": "Sloth",
          "alias": "Of Course I Still Love You!",
          "code": "0e75f734-6276-43aa-904f-375cd08028cbyou!.Of.course.I.still.love:0e75f734-6276-43aa-904f-375cd08028cb//NDL-C-HUNTER'S-DECEMBER",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Messiah"
        },
        {
          "callsign": "Trojan",
          "alias": "Rod Silver",
          "code": "0e19579c-5fba-4fc0-a3c2-1476c62cd935Silver.Rod:0e19579c-5fba-4fc0-a3c2-1476c62cd935//NDL-C-IRON-SKY",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Download More RAM"
        },
        {
          "callsign": "Bastet",
          "alias": "SEKHMET-K4",
          "code": "da58ba8a-32a8-44fe-8316-ecb1aa0448e5.S.K4:da58ba8a-32a8-44fe-8316-ecb1aa0448e5//NDL-C-SECOND-AXE",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Khopesh"
        },

      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Mirrorsmoke",
        "headerSubtitle": "Mercenary Company",
        "subheaderTitle": "Crisis Response",
        "subheaderSubtitle": "Delta-Echo-Echo-Zulu",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: visible;
}
</style>
