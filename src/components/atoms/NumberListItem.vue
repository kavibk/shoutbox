<!-- Component for rendering a single phone number within the NumberList
  component.  Accepts as a prop a string containing a formatted phone number,
  as well as another prop for whether or not this is a duplicate. -->
<template>
  <li class="number-list-item">
    <div>
      <span>{{ number }}</span>
    </div>
  </li>
</template>

<script>
export default {
  name: "NumberListItem",
  props: ['number', 'duplicate']
}
</script>

<style lang="scss" scoped>
.number-list-item {
  list-style: none;
  display: flex;
  align-items: center;
  justify-content: flex-start;

  div {
    background-color: #35cabf;
    border-radius: 8px;
    color: #FFF;
    font-weight: 700;
    padding: 0 10px;
  }
}
</style>
