<template>
  <main class=" bg-slate-900 min-h-screen">
    <div class=" flex items-center justify-center flex-col text-white">
      <h1 class=" text-3xl">Online Voting System</h1>
      <form @submit.prevent="submitVote">
        <h2 class=" items-center justify-center flex text-2xl p-4">Candidates</h2>
        <ul class=" flex flex-row gap-8">
          <li v-for="candidate in candidates" :key="candidate.id">
            
         
            <input type="radio" :value="candidate.id" v-model="selectedCandidate"/> 
              
            {{ candidate.name }}
          </li>
        </ul>
        <button type="submit" class=" my-3 bg-blue-600 rounded justify-self-center items-center flex mx-auto px-8 py-2 text-white">Vote</button>
      </form>
      <h2 class=" text-4xl">Results</h2>
      <ul class=" text-white rounded-md p-3 flex items-center justify-center gap-5">
        <li v-for="candidate in candidates" :key="candidate.id" class=" bg-slate-700  flex flex-col "  >
          {{ candidate.name }}: {{ candidate.votes }} votes
        </li>
      </ul>
     
    </div>
  </main>
</template>

<script>
import { createClient } from '@supabase/supabase-js';

export default {
  data() {
    return {
      selectedCandidate: null,
      candidates: [
        { id: 1, name: 'cosmas', votes: 0 },
        { id: 2, name: 'cheruiyot', votes: 0 },
 
      ],
      voted: false,
      supabase: createClient(
        'https://swocaqwllmwkocyhqnhm.supabase.co',
        'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InN3b2NhcXdsbG13a29jeWhxbmhtIiwicm9sZSI6ImFub24iLCJpYXQiOjE2NzExNzM2NDIsImV4cCI6MTk4Njc0OTY0Mn0.eE7E7QeAT2iGCsInhiPVjYqOEuyrfD0-OAKsKhuWVaI',

      ),
    }; 
  },
  created() {
    this.loadCandidates();
    //this.listenForVotes();
   
  },
  methods: {
    //load candidates from the database
    
    async loadCandidates() {
  const { data: candidates, error } = await this.$supabase.from('candidates').select('*');
  if (error) {
    console.error(error);
  } else {
    this.candidates = candidates.map(candidate => ({ ...candidate, votes: parseInt(candidate.votes) }));
  }
},
    //submit vote to the database
    async submitVote() {
      if (this.voted) {
        alert('You have already voted!');
        return;
      }

      if (!this.selectedCandidate) {
        alert('Please select a candidate to vote!');
        return;
      }
      //insert vote into the database

      const { data, error } = await this.supabase.from('Votes').insert({ candidate_id: this.selectedCandidate });
      if (error) {
        console.error(error);
        alert('Failed to submit vote. Please try again later.');
      } else {
        const selectedCandidate = this.candidates.find(candidate => candidate.id === this.selectedCandidate);
        selectedCandidate.votes++;
        this.voted = true;
        alert('Vote submitted successfully!');
      }
    },
  },
};
</script>
