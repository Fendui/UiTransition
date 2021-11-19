# UiTransition
The UiTransition component is a wrapper component built on Vue's Transition component, which tries to make transitioning between states even more seamless.

## Features ✨

 <ul>
  <li>Create dynamic transitions on the fly</li>
  <li>Save transition presets</li>
 <li>Supports all <code>&lt;Transition&gt;</code> and <code>&lt;TransitionGroup&gt;</code> props</li>
  <li>Supports group transition</li>
 <li>Renders no wrapper element</li>
 </ul>
 
## Props ⚙

<table>
 <thead>
  <tr>
    <th>Name</th><th>Type</th><th>Default</th><th>Description</th>
  </tr>
 </thead>
 <tbody>
  <tr>
    <td>config</td><td><code>[String, Object, Array, Boolean]</code></td><td>'fade'</td><td>This is where you configure the current transition. The values here could be dynamic or static.<br/>All data types accepted, except for <code>Array</code> resolves into an <code>Object</code>. Arrays resolves into an array of objects.<br/><br/><code>String</code>: Use a preset that matches the name. Strings could take in arguments, or stay plane. Eg <code>'fade'</code>, <code>'fade(0, 1)'</code>, <code>'fade({from: 0, to: 1})'</code>. <br/><strong>NB:</strong> when passing arguments to a string value, the referenced saved preset <em>Must</em> be a <code>Function</code>. Also, when passing arguments to a string value, use quotes for string values. Eg <code>fade(var(--from), 1)</code> will throw an error, while <code>fade('var(--from)', 1)</code> wont.<br/><br/><code>Object</code>: When an object is passed, the valid values in the object will be used. Check below for a valid <code>&lt;UiTransition&gt;</code> object value.<br/><br/><code>Array</code>: Using an array value is mostly useful to override a saved preset. Eg <code>['fade', {duration: 400}]</code>. The item(s) with higher array index overrides those with lower indexes.<br/><br/><code>Boolean</code>: Boolean values are mostly used to disable the transition config, or use the default value of 'fade', if the Boolean value is <code>true</code></td>
  </tr>
  <tr>
   <td>Group</td><td><code>Boolean</code></td><td>undefined</td><td>Using this prop will cause the <code>&lt;UiTransition&gt;</code> component to render a <code>&lt;TransitionGroup&gt;</code> component, and key any element without a key.</td>
  </tr>
   <tr>
   <td>Optimise</td><td><code>Boolean</code></td><td>true</td><td>This prop adds the CSS <code>will-change</code> property a frame before transition starts, and removes the property a frame after transition ends.</td>
  </tr>
  <tr>
   <td>previousTransform</td><td><code>String</code></td><td>undefined</td><td>Use this prop to set a previous transform state to be preserved while transitioning, to avoid the ugly transform jumps</td>
  </tr>
 </tbody>
</table>


## Caveats ℹ

<ul><li>The <code>&lt;UiTransition&gt;</code> component is not available as a standalone component for now.</li>
  <li>FendUI tries to give a butter smooth transition, this means we can only work with the 'friendly' transition properties <em>opacity</em> & <em>transform</em></li><li>FendUI <strong>will not</strong> check for a valid CSS value, so using a wrong value might break your transition.</li><li>The <code>&lt;UiTransition&gt;</code> config prop makes use of Vue's <code>&lt;Transition&gt;</code> <code>name</code> prop under the hood.</li></ul>
  

## Config anatomy 🧬

<strong>Robust (with full enter and leave configurations)</strong>

````js
  {
    enter: {
      from: {
        transform: 'scale3d(0, 0, 1)',
        opacity: '0'
      },
      to: {
        transform: 'scale3d(1, 1, 1)',
        opacity: '1'
      },
      duration: '400ms',
      delay: '100',
      ease: 'ease',
      origin: 'center'
    },
    leave: {
      from: {
        transform: 'scale3d(1, 1, 1)',
        opacity: '1'
      },
      to: {
        transform: 'scale3d(0, 0, 1)',
        opacity: '0'
      },
      duration: '300ms',
      delay: '0',
      ease: 'ease-in-out',
      origin: 'top right'
    }    
  }
````

<strong>Simple (with a root configuration, and enter, and leave)</strong>

````js
  {
    enter: {
      from: {
        transform: 'scale3d(0, 0, 1)',
        opacity: '0'
      },
      to: {
        transform: 'scale3d(1, 1, 1)',
        opacity: '1'
      },
    },
    leave: {
      from: {
        transform: 'scale3d(1, 1, 1)',
        opacity: '1'
      },
      to: {
        transform: 'scale3d(0, 0, 1)',
        opacity: '0'
      },
    },
    duration: '400ms',
    delay: '100',
    ease: 'ease',
    origin: 'center'
  }
````

Since most cases of transitions will be that the leave state is a reverse of the enter state, the <code>&lt;UiTransition&gt;</code> <strong>config object</strong> can be further simplified to a flat <code>Object</code>.

<strong>Simplest form</strong>
````js
  // fade
  {
    from: {
      opacity: 0
    }, 
    duration: '400ms',
  }
````

<strong>NB:</strong> Basically, the <code>&lt;UiTransition&gt;</code> config object must have at least a ``from``, and a ``duration`` property, unless you wish to override the preset, by using an ``Array`` config. Eg ``['fade', {duration: 400, to: {opacity: 0.5}}]``

## Valid config ✅

<strong>A simple valid 'static' preset.</strong>

````js
  {
    rotate: {
      from: {
        transform: 'rotate(90deg)',
        opacity: '0'
      },
      duration: '300',
      ease: 'eaee-in-out'
    }
  }
  
  // usage: config='rotate'
````

<strong>A simple valid 'dynamic' preset.</strong>

````js
  {
    rotate: (rotate = '90deg', opacity = '0') => ({
      from: {
        transform: `rotate(${rotate})`,
        opacity
      },
      duration: '300',
      ease: 'eaee-in-out'
    })
  }
  
  // usage: config="rotate('180deg', '0.5')"
````


## Structure 🏗

````vue
  <TransitionGroup v-if='group'>
    <slot />
  </TransitionGroup>
  
  <Transition v-else>
    <slot />
  </transition>
````

## Slots 🎰 

<table>
 <thead>
  <tr>
    <th>Name</th><th>Payload</th>
  </tr>
 </thead>
 <tbody>
  <tr>
    <td>default</td><td>
    <pre><code>{ 
  state: 'beforeEnter' | 'enter' | 'enterCancelled' | 'afterEnter' | first four for 'leave' | first four for 'appear',
  phase: 'enter' | 'leave'
}
</code></pre></td>
 </tbody>
</table>

## Examples 💁‍♀️

 <em>Simplest form</em>
 
````vue
  <div id='app'>
    <UiTransition>
      <div :key='dynamicKey'>
        My key is {{dynamicKey}}
      </div>
    </UiTransition>
  </div>      
````

<em>With a static config of type</em> ``String``
  
````vue
  <div id='app'>
    <UiTransition config='rotate'>
      <div :key='dynamicKey'>
        My key is {{dynamicKey}}
      </div>
    </UiTransition>
  </div>      
````

<em>With a dynamic config of type</em> ``String``
  
````vue
  <div id='app'>
    <UiTransition config='rotate(90deg)'>
      <div :key='dynamicKey'>
        My key is {{dynamicKey}}
      </div>
    </UiTransition>
  </div>      
````

<em>With a static config of type</em> ``Object``
  
````vue
  <div id='app'>
    <UiTransition 
      :config="{
        from: {
          opacity: 0
        },
        duration: 200
      }"
    >
      <div :key='dynamicKey'>
        My key is {{dynamicKey}}
      </div>
    </UiTransition>
  </div>      
````

<em>Overriding a config with type</em> ``Array``
  
````vue
  <div id='app'>
    <UiTransition 
      :config="['rotate', {
        to:{
          transform: 'rotate(10deg)',
        }
      }]"
    >
      <div :key='dynamicKey'>
        My key is {{dynamicKey}}
      </div>
    </UiTransition>
  </div>      
````

<em>Using Vue props</em>
  
````vue
  <div id='app'>
    <UiTransition config='rotate(90deg)' mode='out-in'>
      <div :key='dynamicKey'>
        My key is {{dynamicKey}}
      </div>
    </UiTransition>
  </div>      
````

<em>As a TransitionGroup</em> ``String``
  
````vue
  <div id='app'>
    <UiTransition config='rotate(90deg)' group>
      <div v-for='(item, index) in loopData' :key='index'>
        My key is {{index}}
      </div>
    </UiTransition>
  </div>      
````

## CSS variables 
<table>
 <thead>
  <tr>
    <th>Name</th><th>Default value</th><th>Description</th>
  </tr>
 </thead>
 <tbody>
  <tr>
    <td>--ui-height</td><td>28px</td><td>Use this variable to set the root element's height</td>
  </tr>
   <tr>
     <td>--ui-width</td><td>44px</td><td>Use this variable to set the root element's width</td>
  </tr>
   <tr>
     <td>--ui-track-height</td><td>100%</td><td>Sets the height of the track</td>
  </tr>
  <tr>
     <td>--ui-track-radius</td><td>100%</td><td>Sets the border-radius of the track</td>
  </tr>
 <tr>
     <td>--ui-track-background</td><td>#999</td><td>Sets the background of the track</td>
  </tr>
    <tr>
     <td>--ui-track-background-checked</td><td>#007bff</td><td>Sets the background of the track when the component is active</td>
  </tr>
    <tr>
      <td>--ui-thumb-size</td><td>25px</td><td>Sets the height and width of the <code>.thumb</code> element</td>
  </tr>
    <tr>
      <td>--ui-thumb-radius</td><td>50%</td><td>Sets the border-radius of the <code>.thumb</code> element</td>
  </tr>
    <tr>
      <td>--ui-thumb-offset</td><td>1.5px</td><td>The gap between the thumb and the edges</td>
  </tr>
    <tr>
      <td>--ui-thumb-translatex</td><td>
<pre><code>calc(var(--ui-width) - 
   var(--ui-thumb-size) - 
   var(--ui-thumb-offset)
)</code></pre></td><td>Computes the active state transformation</td>
  </tr>
  <tr>
      <td>--ui-thumb-background</td><td>#fff</td><td>The background of the thumb</td>
  </tr>
 </tbody>
</table>
