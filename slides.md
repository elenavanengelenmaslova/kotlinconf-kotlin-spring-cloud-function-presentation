---
theme: default
colorSchema: light
image: KotlinConfStart.png
layout: image
class: text-center
highlighter: shiki
shiki:
  theme: nord
canvasWidth: 800
lineNumbers: true
drawings:
  persist: false
transition: slide-up
css: unocss
title: 'Kotlin Clean Architecture for Serverless: Business Logic You Can Take Anywhere'
---


<!--
Wow! what a great crowd! Thank you for being here!
next slide

-->


---
layout: center
---

<div class="space-y-4 px-8 text-center">
  <div class="text-4xl font-bold">Is serverless secretly locking us in?</div>
  <div class="text-lg italic text-slate-600">
    That is what many developers and even some thought leaders believe.
  </div>
 <div class="text-2xl">But what if the real problem is not serverless itself but how we architect our code?</div>
</div>


---
transition: fade-out
---

# Building Blocks

<v-clicks>

- 🌱 **Spring Cloud Function**

- 🧼 **Clean Architecture**

- 📦 **Gradle modules**

- 🗝️ **Key Takeaway: A framework for cloud-agnostic business logic**

</v-clicks>

<br>
<br>




<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

<!--

- 1 min
Clean Architecture to isolate business logic from cloud specific code
🌱 Spring Cloud Function to bridge cloud-specific concerns and core logic while preserving separation of concerns
📦 Gradle modules to enforce architectural boundaries
-->

---
class: px-8
---

# Hi, I'm Elena van Engelen 👋

<v-clicks>


<div class="flex flex-col md:flex-row gap-6 items-start pt-6 max-w-6xl">

  <!-- Left: Bullet points including author/blogger -->
  <ul class="text-left text-lg leading-relaxed text-slate-700 space-y-4 flex-1">
    <li><strong>Senior Software Engineer</strong> specializing in Kotlin and cloud-native solutions.</li>
    <li>Currently working as <strong>Lead Engineer</strong> at <strong>AZL (Life & Pensions – NN Group)</strong>.</li>
    <li>4× AWS and 3× Azure certified</li>
   <li>AWS Community Builder in Serverless Category</li>
    <li><strong>Author</strong> of <em>Kotlin Crash Course</em> book and a <strong>blogger</strong> on Kotlin & Cloud topics.</li>
  </ul>

  <!-- Right: Book photo only -->
  <div class="flex-shrink-0">
    <img src="/MeAndBook.jpg" alt="Kotlin Crash Course"
      class="rounded-xl shadow-lg w-[200px] h-auto object-cover" />
  </div>

</div>

</v-clicks>

<!--
- 3- mins
- This is me looking very happy the book is finally finished -  if anyone asks you to write a book - don't do it. It's like giving birth to another child - I already have 2 real children - it only takes longer than 9 months to do it. Really don't do it! Anyway, let's get started...
-->

---
transition: slide-up
layout: image-right
image: /Scaling.png
---

# What is Serverless and FaaS?

<v-clicks>

- No Infrastructure to Manage

- Resources on As-Used Basis

- Scales Automatically

- Triggered by Events

</v-clicks>

<!--
- 2 mins

Serverless doesn’t mean there are no servers — it just means you don’t have to manage them.

No infrastructure to manage:
You don’t need to manage or patch servers. That’s handled by the cloud provider. This frees you up to focus on writing business logic instead of operations.

Resources on as-used basis:
Serverless is very resource-efficient — you only pay for the compute time you actually consume. It scales to zero when there's no traffic, so you're not paying for idle capacity.

Scales automatically:
Serverless functions adjust automatically to load — they scale up during high traffic and back down when it's quiet, without manual intervention.

Triggered by events:
Functions are invoked by events, like HTTP requests, messages, or file uploads. This makes serverless a natural fit for event-driven architectures, integrations, and automation flows.
-->


---
transition: slide-up
layout: image-right
image: /ServerlessEvents.png
---

# Common FaaS Use Cases

<v-clicks>

- REST APIs
- IoT event processing
- Data transformation
- Scheduled tasks

</v-clicks>

<!--
Functions as a Service (FaaS) is the most common model in serverless. You define a function triggered by events, such as HTTP requests, message queues, event streaming or file uploads, and the platform runs it only when needed. Popular use cases include REST APIs, IoT event processing, data transformation, and scheduled tasks.
-->

---
layout: center
---

<div class="space-y-4 px-8 text-center">
  <div class="text-4xl font-bold">Using FaaS makes life easier</div>
  <div class="text-2xl">but what about Portability?</div>
  <div class="text-lg italic text-slate-600">
    Let's see how Spring Cloud Function, Clean Architecture, and Gradle modules make your business logic Cloud-Agnostic!
  </div>
</div>


<!--
FaaS makes life easier. You no longer manage infrastructure; instead, you just write functions, and the cloud takes care of provisioning, scaling, and billing based on usage. It is fast to implement, cost-effective, and ideal for small, focused units of work.

But here is the catch: what about portability? You start with one cloud provider, maybe AWS Lambda or Azure Functions, and before long, your business logic is wrapped in platform-specific code. Companies are concerned about vendor lock-in, or rework, if they ever need to switch providers. This fear keeps many companies from taking full advantage of FaaS.

-->
---
layout: image-right
image: /SpringCloudFunction.png
---
# Spring Cloud Function in a Nutshell

<v-clicks>

- Runs your function as a Spring App

- Spring Boot Features

- Adaptable to Environments

</v-clicks>

<!--
- 2 mins
- Runs Spring app on FaaS: Enables you to Run you function as a Spring app.
- Spring Boot Features: You get dependency injection, config, and metrics even in FaaS.
- Adaptable: Supports running locally or on cloud.

-->


---

# Azure Function Code Examples

## build.gradle.kts in Azure infrastructure module:

```kotlin
implementation("org.springframework.cloud:spring-cloud-function-adapter-azure:4.2.2")
```

---

# Azure Function Code Examples

## Azure function HTTP event handler

```kotlin {all|1,3-10,12}
@FunctionName("UploadDocument")
fun uploadDocument(
    @HttpTrigger(
        name = "request",
        methods = [HttpMethod.POST],
        authLevel = AuthorizationLevel.FUNCTION,
        route = "docs-flow"
    ) originalRequest: HttpRequestMessage<ByteArray>,
    context: ExecutionContext,
): HttpResponseMessage {
    return buildResponse(request)
}
```


<!-- 
- 2mins
-->

---

# Terraform CDK - Azure Functions example

```kotlin {all|11}
val functionApp = LinuxFunctionApp(
    this, "SpringCloudExampleFunctionApp",
    LinuxFunctionAppConfig.builder()
        .name("spring-clean-architecture-fun")
        // fun settings
        .siteConfig(
           // site config   
        )
        .appSettings(
            mapOf(
                "MAIN_CLASS" to "com.example.clean.architecture.Application",
            )
        )
        .build()
)

```

<!--
- 1 min
-->

---

# AWS Lambda Code Examples

## build.gradle.kts in AWS infrastructure module:

```kotlin
implementation("org.springframework.cloud:spring-cloud-function-adapter-aws:4.2.2")
```

---

# AWS Lambda Code Examples

```kotlin {all|2,8}
@Bean
fun uploadDocument(): Function<APIGatewayProxyRequestEvent, APIGatewayProxyResponseEvent> {
    return Function { event ->
        APIGatewayProxyResponseEvent()
            .withStatusCode(200)
            .withBody("Hello KotlinConf 2025!")
    }
}
```


<!-- 
- 2 mins
-->

---

# Terraform CDK - AWS Lambda example

```kotlin {all|5,14,15}
 LambdaFunction(
    this, "Spring-Clean-Architecture-Fun",
    LambdaFunctionConfig.builder()
        .functionName(functionName)
        .handler("org.springframework.cloud.function.adapter.aws.FunctionInvoker")
        .runtime("java21")
        // other settings
        .role(lambdaRole.arn)
        .dependsOn(listOf(productsTable, lambdaRole))
        .environment(
            LambdaFunctionEnvironment.builder()
                .variables(
                    mapOf(
                        "SPRING_CLOUD_FUNCTION_DEFINITION" to "uploadDocument",
                        "MAIN_CLASS" to "com.example.clean.architecture.Application",
                    ).build()
                ).build()
        )
```

<!--
- 1 min
- call AWS and Azure
- Insure to include spring cloud function adapter in infrastructure layer dependencies.
-->

---

# Terraform CDK - Simplified API Gateway example

```kotlin {all|1,5,8,12,13}
val docsFlowResource = ApiGatewayResource(
    this, "DocsFlow-Resource",
    ApiGatewayResourceConfig.builder()
        // ...
        .pathPart("docs-flow")
        .build()
)
val docsFlowMethod = ApiGatewayMethod(
    this, "DocsFlow-Method",
    ApiGatewayMethodConfig.builder()
        // ...
        .httpMethod("POST")  // Only allow POST method
        .apiKeyRequired(true)  // Require API key
        .build()
)
```

<!--
- 1 min
- call AWS and Azure
- Insure to include spring cloud function adapter in infrastructure layer dependencies.
-->

---
preload: false
---

# Spoiler Alert 🚨

Hello World is already racing —  
we’re not going to build it.  
We’re going to upgrade it.

<div class="w-full relative mt-6">
  <div class="relative w-80 h-80">
    <img
      v-motion
      :initial="{ x: 800 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="/F1Car.jpg"
    />
  </div>

  <div
    class="text-5xl absolute bottom-16 left-30 text-[#2B90B6] z-10"
    v-motion
    :initial="{ x: -80, opacity: 0}"
    :enter="{ x: -7, opacity: 1, transition: { delay: 2000, duration: 1000 } }">
    Fast-forward from
  </div>

<div
  class="text-5xl absolute bottom-16 left-[calc(50%+12rem)] z-10 font-mono text-pink-600 tracking-tight"
  v-motion
  :initial="{ y: 100, opacity: 0}"
  :enter="{ y: -4, opacity: 1, transition: { delay: 3500, duration: 1000 } }"
>
  Hello World
</div>


</div>

<script setup lang="ts">
const final = {
  x: 0,
  y: 0,
  rotate: 0,
  scale: 1,
  transition: {
    type: 'spring',
    damping: 10,
    stiffness: 20,
    mass: 2
  }
}

setTimeout(() => {
  const el = document.getElementById('replaceK');
  if (el) el.style.opacity = "0";
}, 3500);
</script>

<!--
- 1 min
- 5 mins demo

Because let's face it — anyone can deploy Hello World.

But before we move on, let’s take a moment to understand what that deployment is actually doing.

Then we’ll live-code how to use Clean Architecture with Spring Cloud Function to inject real business logic into both AWS and Azure — while keeping cloud-specific code separate from the core logic.

-->

---

## Solution Design

<br>

<img src="/SolutionDesign.png" alt="Solution Design" class="max-w-[60%] max-h-[40vh] object-contain mx-auto" />

<!-- 

- 1 mins
(Call Azure)

So we are not using a Hello world, however, the use case is still simple, we have some business logic, which requires some persistence and notification functionality, and we are using a cloud-specific service for this persistence and notification. In AWS we will use S3 and SES, and in Azure we are using Blob Storage and ACS (Azure Communication Services). These are exactly what we need to store our docs and notify the reviewer.
-->


---
class: center
---

# Clean Architecture for Serverless

<img src="/CleanArch.png" alt="Clean Architecture" class="w-4/5 h-auto" />


<!--
- 4 mins
- **Separation of Concerns**: Different aspects of software development (use case business rules, domain logic,
  integration logic) are isolated from each other.

- **Independent Layers**: Changes in one layer (like switching cloud providers) should not affect other layers.

- **Testability**: Because of the clear boundaries and interfaces between layers, testing becomes straightforward.

- **Minimizing Cloud Lock-in**: Easily switch between AWS, Azure, or others, with business logic unaware of the
  underlying cloud provider.

Imagine we are build a pension administration microservice, 
- in domain we might have Participant, ParticipantRelation, PensionFund
- in application we have use case business logic, for example process employment, marriage, divorce, death

-->

---

# Clean Architecture - with gradle modules

```css {all|1-3|4-5,8-9|4,6,8,10}
├── software/              // Holds all the application code
│   ├── domain/
│   ├── application/
│   └── infrastructure/    // Infrastructure specific code
│       ├── aws/         
│       └── azure/       
│
└── cdk/                    // Terraform CDK Kotlin code
    ├── aws/
    └── azure/
```

<!--
- 1 min
Imagine we are build a pension administration microservice, 
- sofware domain we have domain specific code
- in application we have use case business logic, and interfaces 
- infra has integration to our cloud specific service, e.g. Azure blob storage & Service Bus or AWS S3 and Event Bridge
- cdk has cloud specific infrastructure as code

-->

---

# Clean Architecture - With Gradle Modules

How would the module definition look in Gradle Kotlin DSL?

**settings.gradle.kts:**

```kotlin
include(":application")
project(":application").projectDir = file("software/application")
include(":domain")
project(":domain").projectDir = file("software/domain")
// ... other modules
```

**application/build.gradle.kts:**

```kotlin
dependencies {
    implementation(project(":domain"))
}
```

---
preload: false
---

# 🧑‍💻 From Hello World to Business Logic

<div class="w-full relative h-[28rem] flex justify-center items-center">

  <!-- AWS Lambda -->
  <div class="absolute top-[42%] left-[20%] z-0">
    <img src="/AwsLambda.png" class="w-28" />
  </div>

  <!-- Azure Function -->
  <div class="absolute top-[42%] right-[20%] z-0">
    <img src="/AzureFunctions.png" class="w-28" />
  </div>

  <!-- Business Logic flying to AWS Lambda -->
  <div
    class="absolute top-6 left-1/2 -translate-x-1/2 z-10"
    v-motion
    :initial="{ x: 0, y: 0 }"
    :enter="{ 
      x: -190, 
      y: 115,
      transition: { delay: 300, duration: 1000 }
    }"
  >
    <img src="/BusinessLogic.png" class="w-20" />
  </div>

  <!-- Business Logic flying to Azure Function -->
  <div
    class="absolute top-6 left-1/2 -translate-x-1/2 z-10"
    v-motion
    :initial="{ x: 0, y: 0 }"
    :enter="{ 
      x: 115, 
      y: 115,
      transition: { delay: 500, duration: 1000 }
    }"
  >
    <img src="/BusinessLogic.png" class="w-20" />
  </div>

 <!-- Arrow to S3 (above business logic box) -->
<div
  class="absolute top-[12%] left-[26%] z-0"
  v-motion
  :initial="{ opacity: 0 }"
  :enter="{ 
    opacity: 1,
    transition: { delay: 1500, duration: 600 }
  }"
>
  <img src="/ArrowS3.png" class="w-32" />
</div>

<!-- Arrow to SES -->
<div
  class="absolute top-[18%] left-[10%] z-0"
  v-motion
  :initial="{ opacity: 0 }"
  :enter="{ 
    opacity: 1,
    transition: { delay: 1500, duration: 600 }
  }"
>
  <img src="/ArrowSES.png" class="w-32" />
</div>

<!-- Arrow to Blob Storage -->
<div
  class="absolute top-[18%] right-[26%] z-0"
  v-motion
  :initial="{ opacity: 0 }"
  :enter="{ 
    opacity: 1,
    transition: { delay: 1500, duration: 600 }
  }"
>
  <img src="/ArrowBlobStorage.png" class="w-32" />
</div>

<!-- Arrow to ACS -->
<div
  class="absolute top-[11%] right-[8%] z-0"
  v-motion
  :initial="{ opacity: 0 }"
  :enter="{ 
    opacity: 1,
    transition: { delay: 1500, duration: 600 }
  }"
>
  <img src="/ArrowACS.png" class="w-32" />
</div>


</div>



<!--
Let's update our Hello world Lambda and Azure function to use the busness logic which is a WireMock with some forwarding logic for serverless:
- update module dependencies
```kotlin
implementation(project(":domain"))
```
- wire in business logic from functions

```kotlin
private val handleClientRequest: HandleClientRequest,
private val handleAdminRequest: HandleAdminRequest,
```

- Implement Azure client request handling

```kotlin
val response = handleClientRequest(
            HttpRequest(
                org.springframework.http.HttpMethod.valueOf(request.httpMethod.name),
                request.headers,
                route,
                request.queryParameters,
                request.body
            )
        )
```

- Implement Azure wiremock response handling

```kotlin
val response = handleAdminRequest(
            route ?: "",
            HttpRequest(
                SpringHttpMethod.valueOf(request.httpMethod.name),
                request.headers,
                route,
                request.queryParameters,
                request.body
            )
        )
```

- map wiremock response to azure api response

```kotlin
.createResponseBuilder(HttpStatus.valueOf(response.httpStatusCode.value()))
            .let { responseBuilder ->
                var builder = responseBuilder
                response.headers?.forEach { header ->
                    header.value.forEach {
                        builder = builder.header(header.key, it)
                    }
                }
                builder
            }
            .body(response.body)

```
- Add dependency and inject our request handling functions into AWS Lambda
- Convert our Lambda specific request to our domain object

```kotlin
 private fun APIGatewayProxyRequestEvent.createHttpRequest(path: String): HttpRequest {
        val request = HttpRequest(
            method = HttpMethod.valueOf(httpMethod),
            headers = headers,
            path = path,
            queryParameters = queryStringParameters.orEmpty(),
            body = body
        )
        return request
    }
```

- call injected function and convert response

```kotlin
  with(event) {
                logger.info { "MockNest request: $httpMethod $path $headers" }
                if (path.startsWith(ADMIN_PREFIX)) {
                    val adminPath = path.removePrefix(ADMIN_PREFIX)
                    handleAdminRequest(adminPath, createHttpRequest(adminPath))
                } else {
                    handleClientRequest(createHttpRequest(path.removePrefix(MOCKNEST_PREFIX)))
                }
            }.let {
                APIGatewayProxyResponseEvent()
                    .withStatusCode(it.httpStatusCode.value())
                    .withHeaders(it.headers?.toSingleValueMap())
                    .withBody(it.body?.toString().orEmpty())
            }
```


Let's update our Hello world Lambda and Azure function to use the busness logic which is a WireMock with some forwarding logic for serverless: 
- inject repository
```kotlin
private val objectStorage: ObjectStorageInterface,
```

- call business logic from functions

- add stub mapping
```kotlin
 private fun saveStubMapping(mapping: StubMapping, bodyString: String, ): String {
        return mapping.runCatching {
            if (isPersistent) {
                logger.info { "Saving persistent mapping with ID: $id" }
                objectStorage.saveMapping(id.toString(), bodyString).let { "Saved mapping $it" }
            } else "Mapping ${mapping.id} not persistent"
        }.onFailure {
            logger.error(it) { "Failed to check or save persistent mapping: ${it.message}" }
        }.getOrThrow()
    }

 saveStubMapping(mapping, bodyString)
```

- delete all mappings when we call reset
```kotlin
 // Delete all mappings from storage
                    val storedMappings = objectStorage.listMappings()
                    storedMappings.forEach { mappingId ->
                        logger.info { "Deleting stored WireMock mapping with ID: $mappingId" }
                        objectStorage.deleteMapping(mappingId)
                    }
```

- build, commit and push


-->

---
layout: image-right
image: /TerraformCDK.png
---

# Terraform CDK

<v-clicks>

- **Multi language support**

- **Multi-Cloud Compatibility**

- **Reusability**

- **Predictable Changes**

</v-clicks>

<!--
- 1 min
**Multi language support**: Utilize familiar programming languages like Kotlin, Java or TypeScript for infrastructure
  code.

- **Multi-Cloud Compatibility**: Define and provision infrastructure seamlessly across multiple cloud providers like
  AWS, Azure, and Google Cloud.

- **Reusability**: Leverage constructs (modules) to create reusable, shareable, and composable components.

- **Predictable Changes**: Employ `cdktf diff` and to understand the changes.

-->

---

# Deployment

```yaml {all|4-5|10-12}
- name: Generate Terraform files
  run: |
    cd ${GITHUB_WORKSPACE}/cdk
    cdktf get
    cdktf synth

- name: Deploy with Terraform
  run: |
    cd ${GITHUB_WORKSPACE}/cdk/cdktf.out/stacks/${{ matrix.config.stack-name }}
    terraform init -reconfigure
    terraform plan -out=tfplan
    terraform apply -auto-approve tfplan
```

<!--
- 1 m
(Run AWS)
Generate Terraform Files

- cdktf get: Fetches the dependencies required for the Terraform CDK code, such as necessary Terraform providers and modules referenced in your CDKTF code.
- cdktf synth: Converts your Kotlin CDKTF code into Terraform JSON files.

Deploy with Terraform

- terraform init -reconfigure

Initializes the Terraform working directory (downloads providers, configures backends).
-reconfigure forces Terraform to ignore any previously saved backend config and re-read it from main.tf.json.
If you're using a remote backend like S3 (AWS), this ensures Terraform reads the real current state from there, instead of looking at a local .tfstate file.
- terraform plan -out=tfplan

Creates an execution plan by comparing the desired state (your Terraform files) with the current state (from the backend).
The plan is saved to a file called tfplan.
This file will then be used for the actual apply step, so you're guaranteed to apply exactly what was planned.

- terraform apply -auto-approve tfplan

Applies the previously generated plan (tfplan) without prompting for approval. What could possibly go wrong? ;)
This ensures only the changes you already reviewed or tested in the plan step are applied — no surprises.
-->

---
layout: center
---

# Demo 👀

<!--
- 5 mins

-->

---


# Key Takeaways

🧭 Recipe for Cloud-Agnostic Serverless FaaS

<v-clicks>

1️⃣ **Clean Architecture**: Business logic depends on interfaces, not cloud code

2️⃣ **Spring Cloud Function**: Abstractions follow use cases, not platform details

3️⃣ **Gradle Modules**: Enforces separation between layers

💡 **Kotlin** can target JVM 8+ and **Terraform CDK** shares one language across clouds

</v-clicks>

<!-- 
- 2 mins

1️⃣ **Clean Architecture**: Business logic depends on interfaces, not cloud code. Use architecture such as clean architectures to Separate cloud-specific code from business logic. Use DI to inject cloud-specific dependencies — keep the logic clean 


2️⃣ **Spring Cloud Function**: Abstractions follow use cases, not platform details. Don't design for a cloud, design for what your business logic needs. Your interface defines the contract; the cloud just fulfills it

3️⃣ **Gradle Modules**: Enforces separation between layers. This stops accidental imports and helps you isolate your layers.

💡 **Kotlin** can target JVM 8+ and **Terraform CDK** shares one language across clouds. You can use the latest version of Kotlin independently of which JVM the current cloud provider supports. Though we need to use cloud-specific constructs, we can still use the same language. 

Alright — that wraps up my talk. If you learned something new, raise your hand.”
(pause, smile)
“Amazing! Now… could you smile? I want to take a selfie to show my 12-year-old that someone actually pays attention when I speak.”


-->

---
class: flex flex-col items-center justify-start h-[100vh] pt-8 space-y-4 px-8
---

# ❓ Q&A

<div class="text-base text-slate-500 text-center mt-2">
Feel free to ask anything — architecture, Kotlin, or serverless!
</div>

<div class="flex flex-row justify-center gap-20 pt-6">

  <!-- Left: Connect with me -->
  <div class="flex flex-col items-center space-y-2 text-sm text-slate-500">
    <img src="/website-qr.png" alt="QR code to personal website" class="w-28 rounded shadow" />
    <div><strong>Connect with me</strong> 🌐</div>
    <a href="https://elenavanengelenmaslova.github.io/" target="_blank" class="text-blue-600 underline">
      elenavanengelenmaslova.github.io
    </a>
  </div>

  <!-- Right: Medium -->
  <div class="flex flex-col items-center space-y-2 text-sm text-slate-500">
    <img src="/MediumCleanArchitectureArticle.png" alt="QR code to Medium" class="w-28 rounded shadow" />
    <div><strong>Want to go further?</strong> Read the full guide on Medium 🧑‍💻</div>
    <a href="https://qrco.de/bekJvR" target="_blank" class="text-blue-600 underline">
      qrco.de/bekJvR
    </a>
  </div>

</div>



<!-- 
At the end of my Kotlin Crash Course book, chapter 13 walks you through building and deploying an event-driven serverless app with Kotlin and http4k on AWS.

And if you’d like to stay in touch or ask follow-up questions after today, feel free to connect with me through my website or socials.
-->

---
image: KotlinConfEnd.png
layout: image
---
