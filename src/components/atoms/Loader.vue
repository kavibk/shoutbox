<template>
	<div class="loader">Loading...</div>
</template>

<script>
export default {
	name: "Loader"
}
</script>

<style lang="scss" scoped>
@import '../../styles/colors.scss';
.loader,

.loader:after {
  border-radius: 50%;
  width: 2rem;
  height: 2rem;
}
.loader {
  font-size: 1rem;
  position: relative;
  text-indent: -9999em;
  border-top: 0.2rem solid $kavi-blue;
  border-right: 0.2rem solid $kavi-blue;
  border-bottom: 0.2rem solid $kavi-blue;
  border-left: 0.2rem solid $white;
  -webkit-transform: translateZ(0);
  -ms-transform: translateZ(0);
  transform: translateZ(0);
  -webkit-animation: load8 1.1s infinite linear;
  animation: load8 1.1s infinite linear;

	&.center {
		margin: 0 auto;
	}

	&.slight-padding-vertical {
		margin: 1rem 0;
	}
}
@-webkit-keyframes load8 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
@keyframes load8 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
</style>
