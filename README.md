# Design to Compose Recipe

### Step 1: Preparing Ingredients
- Extract any assets you see in the design (i.e., images and icons) and the tokens of the design system (i.e., colors and font family). To do this, you can utilize the following resources:
  - *Images*: Cut the desired image and give it to Google Lens or AI chatbots to find the exact or similar one.
  - *Icons*: Try [this](https://composeicons.com/) or utilize an AI chatbot as you would for images.
  - *Colors*: Use color picker tools and name them with consideration for responsibility.
  - *Font family: Use [this](https://fonts.google.com/) or utilize an AI chatbot to identify the name.
  - **Why?** When building a design, your focus must only be on implementation, not anything else.

### Step 2: Put Related Ingredients Together
- With the resources obtained, implement the design system in [Compose](https://developer.android.com/develop/ui/compose/designsystems/custom).
  - **Why?** This allows you to easily provide styles to individual components and change them later as needed.

### Step 3: Compounding Ingredients and Cooking
- First, examine the screen design with the KISS principle (except "K" is replaced with "L" — i.e., Look, it's simple and stupid).
  - **Why?** So you can believe you can implement it with existing APIs, especially for animations.
  - **Example:** Consider [this](https://dribbble.com/shots/20307962-Cookie-Delivery-App) cookie filter bar animation. Instead of seeing a black bar floating between items from the background and resizing itself, see the black bar individually settled in each filter bar, revealing itself from the right or left side when tapped.

- Break down the screen into individual components and write them cohesively with minimal dependencies and a [single objective](https://github.com/androidx/androidx/blob/androidx-main/compose/docs/compose-component-api-guidelines.md) (Component's purpose section). Also, start with repeated ones in their dedicated Kotlin files.
  - **Why?** This approach helps you understand what you're doing and the next step, making it easy to read and change with minimal effort in the future.
  - **Example:** 
    ```kotlin
    // ❌ Don't do
    // File name: MyScreen.kt
    
    @Composable
    fun MyButton(
      onClick: () -> Unit,
      iconInnerPadding: PaddingValue = PaddingValues(0.dp),
      modifier: Modifier = Modifier,
      icon: Icon? = null,
      contentDescriptor: String? = null,
      text: String? = null,
    ) {
      Row(modifier.clickable(onClick), ...) {
        if (icon != null) Icon(icon, contentDescriptor, modifier = Modifier.padding(iconInnerPadding))
        if (text != null) Text(text, modifier = Modifier.padding(start = if (icon == null) 0.dp else 8.dp))
      }
    }

    
    // ✅ Do instead:
    // File name: MyButton.kt
    
    @Composable
    fun TextButton(onClick: () -> Unit, text: String, modifier: Modifier = Modifier) {
      Row(modifier.clickable(onClick), ...) {
        Text(text)
      }
    }

    @Composable
    fun IconButton(
      onClick: () -> Unit,
      icon: Icon,
      contentDescriptor: String,
      modifier: Modifier = Modifier,
      innerPadding: PaddingValue = PaddingValues(0.dp),
      content: @Composable () -> Unit = { }
    ) {
      Row(modifier.clickable(onClick), ...) {
        Icon(icon, contentDescriptor, modifier = Modifier.padding(innerPadding))
        content()
      }
    }
    ```
- First, create necessary data models and then a Composable function to avoid using hardcoded values inside the function.
  - **Why?** Data and information drive our decisions and make it easy to change data, especially when defining previews.
- Recognize component structure and write layout and element nodes with minimal configuration (i.e., only passing data to required arguments).
  - **Why?** This prevents you from having to redo configurations if they don't suit the next element.
  - **Example:**
    ```kotlin
    @Immutable
    data class Question(val text: String, val possibleAnswers: List<String>, val correctAnswer: String)

    // Build UI structure
    @Composable
    fun QuestionAnswerBox(question: Question) {
      Column {
        Text(introduceText)
        Spacer(Modifier)
        question.possibleAnswers.forEach {
          Row {
            RadioButton(
              selected = question.correctAnswer == it,
              onClick = { }
            )
            Text(it)
          }
        }
      }
    }
    ```

### Step 4: Decorating and Serving
  - Decorate the components and layouts by first manipulating optional arguments and then passing modifiers.
    - **Why?** Optional parameters are a core part of the component and provide better control over component appearance.
    - **Example:**
      ```kotlin
      @Immutable
      data class Question(val text: String, val possibleAnswers: List<String>, val correctAnswer: String)
      
      // First apply optional arguments
      @Composable
      fun QuestionAnswerBox(question: Question) {
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
          Text(introduceText, color = Color.Red)
          Spacer(Modifier)
          question.possibleAnswers.forEach {
            Row(horizontalArrangement = Arrangement.spaced(8.dp)) {
              RadioButton(
                selected = question.correctAnswer == it,
                onClick = { }
              )
              Text(it)
            }
          }
        }
      }
  
      // Finally, decorate with modifiers
      @Composable
      fun QuestionAnswerBox(question: Question) {
        Column(horizontalAlignment = Alignment.CenterHorizontally, modifier = Modifier.background(Color.White)) {
          Text(introduceText, color = Color.Red)
          Spacer(Modifier.height(16.dp))
          question.possibleAnswers.forEach {
            Row(horizontalArrangement = Arrangement.spaced(8.dp)) {
              RadioButton(
                selected = question.correctAnswer == it,
                onClick = { }
              )
              Text(it)
            }
          }
        }
      }
      ```
  - Finally, define mock data and preview components, and amend their code if necessary.
    - **Why?** This way, minor changes won't require extensive adjustments.
  - Enjoy combining components and building the final screen.
    - **Why?** Because why not 😂
