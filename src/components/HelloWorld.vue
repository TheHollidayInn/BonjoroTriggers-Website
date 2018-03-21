<template lang="pug">
  .trigger-editor
    transition(name="slide-fade")
      .row(v-show='!selectedTrigger')
        .col-12.col-md-6.offset-md-3
          h2.trigger-title
            | Triggers
            button.btn.btn-primary(@click='add()') Add a Trigger
          ul.list-group
            li.list-group-item(v-for='(trigger, index) in triggers', :key='trigger._id')
              span {{trigger.name}}
              ul
                li(v-for='condition in trigger.conditions') {{condition.item.text}} {{condition.compare}} {{condition.expectedValue}}
              button.btn.btn-danger(@click="removeTrigger(trigger)") Remove
              button.btn.btn-secondary(@click="cloneTrigger(trigger)") Clone
              button.btn.btn-primary(@click="selectTrigger(trigger)") Edit
    transition(name="slide-fade")
      .row(v-show='selectedTrigger')
        .col-12.col-md-6.offset-md-3
          h2 Current Trigger
          button.btn.btn-primary.finish-button(@click="finish('')") Finish Editing
          .condition(v-for='(condition, index) in conditions')
            h5(v-if='index === 0') When
            h5(v-if='index > 0') and
            .row
              .col-12.col-md-4
                model-select(:options="options",
                                v-model="condition.item",
                                placeholder="select item")
              .col-12.col-md-4
                select.form-control(v-model='condition.compare', v-if='exampleObject[condition.item.text] instanceof Date === false')
                  option =
                  option >
                  option >=
                  option <
                  option <=
                  option !=
                select.form-control(v-model='condition.compare', v-if='exampleObject[condition.item.text] instanceof Date')
                  option more than
                  option exactly
                  option less than
                  option after
                  option on
                  option before
              .col-12.col-md-4
                select.form-control(v-model='condition.expectedValue', v-if='typeof exampleObject[condition.item.text] === "boolean"')
                  option(value='"true"') True
                  option(value='"false"') False
                input.form-control(v-model='condition.expectedValue', type='text', v-if='typeof exampleObject[condition.item.text] === "string"')
                input.form-control(v-model='condition.expectedValue', type='date', v-if='exampleObject[condition.item.text] instanceof Date && ["after", "on", "before"].indexOf(condition.compare) !== -1')
                input.form-control(v-model='condition.expectedValue', type='number', v-if='exampleObject[condition.item.text] instanceof Date && ["after", "on", "before"].indexOf(condition.compare) === -1')
                span(v-if='exampleObject[condition.item.text] instanceof Date && ["after", "on", "before"].indexOf(condition.compare) === -1') days ago
                input.form-control(v-model='condition.expectedValue', type='number', v-if='typeof exampleObject[condition.item.text] === "number"')
            button.btn.btn-danger(@click="remove(condition)") Remove
          .row.actions
            .col-12.text-center
              button.btn.btn-primary(@click="addAnother()") Add a Condition
          //.row.action
            .col-12.text-center
              h2 Then Email Me
</template>

<script>
import axios from 'axios'
import { ModelSelect } from 'vue-search-select'
const uuidv1 = require('uuid/v1')

const keysToLabels = {
  firstName: 'First name',
  lastName: 'Last name',
  name: 'Name',
  conversationRating: 'Conversation Rating',
  email: 'Email',
  phone: 'Phone'
}

export default {
  name: 'HelloWorld',
  components: {
    ModelSelect
  },
  data () {
    return {
      triggers: [],
      selectedTrigger: '',
      conditions: [],
      exampleObject: {
        'First name': 'Mike',
        'Last name': 'Roberts',
        Name: 'Mike Roberts',
        'Conversation Rating': '',
        Email: 'mike@bonjoro.com',
        Phone: '',
        'User ID': '78964335-4c42-4f6c-aa8b-41a77665042c',
        'First Seen (AEDT)': new Date(),
        'Signed up (AEDT)': new Date(),
        'Last seen (AEDT)': new Date(),
        'Last contacted (AEDT)': new Date(),
        'Last heard from (AEDT)': '',
        'Last opened email (AEDT)': new Date(),
        'Last clicked on link in email (AEDT)': new Date(),
        'Web sessions': '150',
        Country: 'Australia',
        Region: 'New South Wales',
        City: 'Hamilton',
        Timezone: 'Australia/NSW',
        'Browser Language': 'en',
        'Language Override': '',
        Browser: 'dalvik',
        'Browser Version': '2.1.0',
        'OS': '',
        'Twitter Followers': '',
        'Job Title': '',
        Segment: '"Active,App installed,M test,Android/Not Paying"',
        Tag: '"bonjoro_watched,bonjoro_sent,bonjoro_reacted,bonjoro_shared,[Issue] Upload Slow,M test"',
        'Unsubscribed from Emails': false,
        'Marked email as spam': false,
        'Has hard bounced': false,
        job_title: 'Tech Wizard',
        api_key: '',
        greetCount: '231',
        appInstalled: '1',
        updatedProfile: '1',
        addedIntegration: '1',
        anonymousId: 'c95a3120-f530-41c7-80d0-eca26baf3980',
        userId: '78964335-4c42-4f6c-aa8b-41a77665042c',
        id: 'c349d35c-572d-4bbe-8981-2de74d186266',
        email: 'mike@bonjoro.com',
        name: 'Bonjoro',
        referral_url: 'https://www.bonjoro.com/r/sqkUtPHOS0R',
        phone_number: '',
        phone_type: '',
        signup_source: 'unknown',
        auth_type: 'password',
        traffic_referrer: '',
        traffic_landing: '',
        referrals_accepted: '',
        greets_completed: '492.0',
        lifetime_revenue: '',
        exception_count: '',
        onboarding_completed: true,
        autologin_url: '',
        'last_greet_uploaded_at (AEDT)': new Date(),
        added_integration: true,
        app_installed: true,
        autologin_app_url: 'https://bonjoro.app.link/VHd5UOtohB',
        autologin_site_url: 'https://www.bonjoro.com/autologin/FKMPXdyW-cp/78964335-4c42-4f6c-aa8b-41a77665042c',
        createdAt: '1462982948',
        created_profile: true,
        rate_opens: '78',
        rate_views: '47',
        referrals_count: '0',
        stat_opens: '429',
        stat_reactions: '81',
        stat_views: '463',
        added_design: true,
        custom_mailer: true,
        article_id: '',
        Unwatched_Bonjoro: '',
        firstName: 'Mike',
        lastName: 'Roberts',
        automations_added: true,
        automations_disabled: '1',
        automations_enabled: '3',
        date_card_added: '',
        integrations_count: '18',
        rate_percentage_clicks: '8',
        rate_percentage_opens: '80',
        rate_percentage_reactions: '16',
        rate_percentage_views: '48',
        'signup_date_at (AEDT)': new Date(),
        stats_clicks: '55',
        stats_opens: '420',
        stats_reactions: '81',
        stats_unique_clicks: '38',
        stats_unique_opens: '392',
        stats_unique_reactions: '81',
        stats_unique_views: '234',
        stats_views: '454',
        team_id: 'c349d35c-572d-4bbe-8981-2de74d186266',
        total_greets_created: '474',
        user_email: 'mike@bonjoro.com',
        user_name: 'Mike Roberts',
        user_phone_type: 'android',
        bonjorourl: '',
        bonjoroassignee: '',
        date_cancelled: '',
        date_subscription_ended: '',
        'Last seen on iOS (AEDT)': '',
        'iOS sessions': '0',
        'iOS App version': '',
        'iOS Device': '',
        'iOS OS version': '',
        'Enabled Push Messaging': '',
        'Is Mobile Unidentified': '',
        'Company name': 'Bonjoro',
        'Company ID': 'c349d35c-572d-4bbe-8981-2de74d186266',
        'Company last seen (AEDT)': new Date(),
        'Company created at (AEDT)': new Date(),
        People: '18',
        'Company web sessions': '1',
        Plan: 'usd-pro-monthly-45',
        'Monthly Spend': '0',
        'Company Segment': '"Active"',
        'Company tag': '',
        'Company size': '',
        'Company industry': '',
        'Company website': '',
        account_credit: '3500',
        monthly_spend: '',
        owner: '174d5e13-333d-4b9e-b268-9300acfd9517',
        paid_account: false,
        plan: 'usd-default-monthly',
        public_billing_url: 'https://www.bonjoro.com/payments/c349d35c-572d-4bbe-8981-2de74d186266',
        stripe_id: 'cus_9HVVc2NEYaSgl9',
        'createdAt (AEDT)': new Date(),
        monthlySpend: '0',
        creation_source: 'api',
        credit_card_added: false,
        on_trial: true
      }
    }
  },
  async mounted () {
    // const localTriggers = localStorage.getItem('bonjoro-triggers')
    // if (localTriggers) {
    //   this.triggers = JSON.parse(localTriggers)
    // }
    const response = await axios.get('https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers')
    this.triggers = response.data.triggers
  },
  computed: {
    fieldOptions () {
      return Object.keys(this.exampleObject)
    },
    options () {
      const options = this.fieldOptions.map(item => {
        const text = keysToLabels[item] ? keysToLabels[item] : item
        return {
          value: item.replace(/\s+/g, '-').toLowerCase(),
          text
        }
      })
      return options
    }
  },
  methods: {
    async add () {
      const triggerID = uuidv1()
      const name = `Trigger ${triggerID}`
      const conditions = [
        {
          _id: uuidv1(),
          item: {value: '', text: ''},
          compare: '=',
          expectedValue: ''
        }
      ]
      const newTrigger = {
        _id: triggerID,
        name,
        conditions
      }

      this.triggers.push(newTrigger)
      this.conditions = newTrigger.conditions

      const response = await axios.post('https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers', {
        name,
        conditions
      })

      newTrigger._id = response.data.trigger._id

      this.selectedTrigger = newTrigger
    },
    selectTrigger (trigger) {
      this.selectedTrigger = trigger
      this.conditions = trigger.conditions
    },
    async cloneTrigger (trigger) {
      const triggerID = uuidv1()
      const name = `Trigger ${triggerID}`

      const cloned = Object.assign({}, trigger)
      cloned.conditions.forEach(con => delete con._id)

      const response = await axios.post('https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers', {
        name,
        conditions: cloned.conditions
      })

      const newTrigger = response.data.trigger

      this.selectedTrigger = newTrigger
      this.conditions = newTrigger.conditions
      this.triggers.push(newTrigger)
    },
    addAnother () {
      const condition = {
        _id: uuidv1(),
        item: {value: '', text: ''},
        compare: '=',
        expectedValue: ''
      }
      this.conditions.push(condition)
    },
    remove (con) {
      const conditionId = con._id
      this.conditions = this.conditions.filter(condition => condition._id !== conditionId)
    },
    removeTrigger (triggerRemove) {
      if (!confirm('Are you sure you want to remove this trigger?')) return
      this.triggers = this.triggers.filter(trigger => trigger._id !== triggerRemove._id)
      axios.delete(`https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers/${triggerRemove._id}`)
      // localStorage.setItem('bonjoro-triggers', JSON.stringify(this.triggers))
    },
    finish () {
      this.selectedTrigger.conditions = this.conditions

      axios.put(`https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers/${this.selectedTrigger._id}`, {
        conditions: this.selectedTrigger.conditions
      })

      this.conditions = []
      this.selectedTrigger = ''

      // localStorage.setItem('bonjoro-triggers', JSON.stringify(this.triggers))
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    margin: 0 10px;
  }
  a {
    color: #42b983;
  }

  .trigger-editor {
    margin-top: 2em;
  }

  .list-group-item button {
    margin-left: .5em;
  }

  .trigger-title button {
    margin-left: .5em;
  }

  .action {
    margin-top: 2em;
  }

  .actions {
    margin-top: 1em;
    margin-bottom: 2em;
  }

  .actions .btn-danger {
    margin-right: 1em;
  }

  .condition {
    margin-bottom: 2em;
  }

  .btn-primary {
    background: #27b3c1;
    box-shadow: inset 0 -3px rgba(0,0,0,.3);
  }

  .btn-danger {
    background: #fc5a00;
    border-color: #c94800;
    box-shadow: inset 0 -3px rgba(0,0,0,.3);
  }

  .btn-secondary {
    box-shadow: inset 0 -3px rgba(0,0,0,.3);
  }

  .finish-button {
    margin-bottom: 2em;
  }

  /* Enter and leave animations can use different */
  /* durations and timing functions.              */
  .slide-fade-enter-active {
    transition: all .3s ease;
  }
  .slide-fade-leave-active {
    transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0);
  }
  .slide-fade-enter, .slide-fade-leave-to
  /* .slide-fade-leave-active below version 2.1.8 */ {
    transform: translateX(10px);
    opacity: 0;
  }
</style>
