<script>
import _ from 'lodash';

const containerToRightEdge = ({ x, width }) => x + width;

export default {
  data() {
    return {
      colors: Object.values({
        aqua: '#00ffff',
        brown: '#a52a2a',
        darkblue: '#00008b',
        darkcyan: '#008b8b',
        darkgreen: '#006400',
        darkkhaki: '#bdb76b',
        darkmagenta: '#8b008b',
        darkolivegreen: '#556b2f',
        darkorange: '#ff8c00',
        darkorchid: '#9932cc',
        darkred: '#8b0000',
        darksalmon: '#e9967a',
        green: '#008000',
        indigo: '#4b0082',
        khaki: '#f0e68c',
        lightblue: '#add8e6',
        lightgreen: '#90ee90',
        lightpink: '#ffb6c1',
        lime: '#00ff00',
        maroon: '#800000',
        navy: '#000080',
        olive: '#808000',
        orange: '#ffa500',
        pink: '#ffc0cb',
        purple: '#800080',
        red: '#ff0000',
      }),
    };
  },
  computed: {
    isOutOfColors() {
      return this.colors.length === 0;
    },
  },
  methods: {
    createNewContainer(containers, dimensions, maxContainerDimensions) {
      let nextX = (
        _(containers)
          .map(containerToRightEdge)
          .max()
      );

      if (!nextX || nextX >= maxContainerDimensions.width) {
        nextX = 0;
      }

      const newContainer = {
        color: this.colors.shift(),
        x: nextX,
        y: 0,
        ...dimensions,
        isOverlapping: false,
      };
      newContainer.isOutOfBounds = this.getIsContainerOutOfBounds(
        newContainer,
        maxContainerDimensions,
      );

      return newContainer;
    },
    getContainerDimensionsAfterRotation(container) {
      const { width, height } = container;

      return {
        width: height,
        height: width,
      };
    },
    getContainerPositionAfterMove(container, direction, maxContainerDimensions) {
      const { width, height } = container;
      let { x, y, isOutOfBounds } = container;

      switch (direction) {
        case 'up':
          if (y > 0) {
            y -= 1;
          }
          break;
        case 'down':
          if (y + height < maxContainerDimensions.height) {
            y += 1;
          }
          break;
        case 'left':
          if (x > 0) {
            x -= 1;
            isOutOfBounds = x + width > maxContainerDimensions.width;
          }
          break;
        case 'right':
          if (x + width < maxContainerDimensions.width) {
            x += 1;
          }
          break;
        default:
      }

      return {
        x,
        y,
        isOutOfBounds,
      };
    },
    getIsContainerOverlapping(containers, container, containerIndex) {
      const {
        x: containerLeft,
        y: containerTop,
        width: containerWidth,
        height: containerHeight,
      } = container;
      const containerRight = containerLeft + containerWidth;
      const containerBottom = containerTop + containerHeight;

      return containers.reduce(
        (isOverlapping, nextContainer, index) => {
          if (isOverlapping || index === containerIndex) {
            return isOverlapping;
          }

          const {
            x: nextContainerLeft,
            y: nextContainerTop,
            width,
            height,
          } = nextContainer;
          const nextContainerRight = nextContainerLeft + width;
          const nextContainerBottom = nextContainerTop + height;

          return (
            containerRight > nextContainerLeft
            && containerLeft < nextContainerRight
            && containerBottom > nextContainerTop
            && containerTop < nextContainerBottom
          );
        },
        false,
      );
    },
    getIsContainerOutOfBounds(container, maxContainerDimensions) {
      return (
        container.x + container.width > maxContainerDimensions.width
        || container.y + container.height > maxContainerDimensions.height
      );
    },
    replaceColor(color) {
      this.colors.push(color);
    },
  },
};
</script>
