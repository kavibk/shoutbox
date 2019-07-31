<!-- Component for rendering a single phone number within the NumberList
  component.  Accepts as a prop a string containing a formatted phone number,
  as well as another prop for whether or not this is a duplicate. -->
<template>
  <li>
    <div class="list-content">
      {{ number }}
    </div>
  </li>
</template>

<script>
export default {
  name: "NumberListItem",
  props: ['number', 'duplicate']
}
</script>
