<template>
  <div>
    <h4>{{ placeDetails.name }}</h4>
    <div class="container">
      <div class="row">
        <div class="col-lg-6">
          <img :src="placeDetails.preview.source" class="img-fluid rounded" alt="Place Image" />
          <br /><br />
          <a
            :href="`https://www.google.com/maps?q=${placeDetails.point.lat},${placeDetails.point.lon}`"
            target="_blank"
            class="btn btn-primary mt-3"
          >
            Find the path in Google Maps
          </a>
        </div>
        <div class="col-lg-6">
          <h6 class="mt-3">Comments</h6>
          <div class="col-lg-12">
            <textarea v-model="newCommentText" class="form-control" placeholder="Add comment"></textarea>
            <button @click="addComment" class="btn btn-primary mt-2" :disabled="newCommentText.trim() === ''">Send</button>
            <p class="text-danger mt-2" v-if="commentError">Please enter a comment.</p>
            <h6 class="mt-3" v-if="comments.length > 0">Visitor Comments</h6>
            <ul class="list-group" v-if="comments.length > 0">
              <li v-for="(comment, index) in comments" :key="index" class="list-group-item">{{ comment.comments }}</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    placeDetails: Object,
  },
  data() {
    return {
      newCommentText: '',
      comments: [], // Store comments from the API here
      commentError: false, // Added to track comment validation
    };
  },
  methods: {
    async addComment() {
      // Check if the comment is empty
      if (this.newCommentText.trim() === '') {
        this.commentError = true;
        return;
      }

      try {
        // Send the new comment to the API
        const response = await axios.post('https://localhost:7189/api/Comments', { comments: this.newCommentText, refId: this.placeDetails.xid });

        if (response.status === 200 || response.status === 201) {
          // If the comment is successfully added to the API, update the comments list
          this.comments.push({ comments: this.newCommentText });
          this.newCommentText = ''; // Clear the input field
          this.commentError = false; // Reset the comment error
        } else {
          console.error('Failed to add comment');
        }
      } catch (error) {
        console.error('Error adding comment:', error);
      }
    },
    async monitorPlaceDetails() {
      // Fetch comments from the API when placeDetails changes
      try {
        const response = await axios.get('https://localhost:7189/api/Comments');

        if (response.status === 200) {
          this.comments = response.data.filter(comment => comment.refId === this.placeDetails.xid);
        } else {
          console.error('Failed to fetch comments');
        }
      } catch (error) {
        console.error('Error fetching comments:', error);
      }
    },
  },
  async mounted() {
    await this.monitorPlaceDetails();
  },
  watch: {
    'placeDetails.xid': 'monitorPlaceDetails',
  },
};
</script>
