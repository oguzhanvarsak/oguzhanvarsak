---
layout: post
title: "Clean Architecture for SwiftUI"
date: 2019-11-04 14:30:00 +0300
description: "Are VIPER, RIBs, MVVM or VIP suitable for SwiftUI project?"
tags: [iOS,swift,SwiftUI,Combine,design,pattern,redux,unidirectional,data,flow,best,practices]
comments: true
sharing: true
published: true
img: clean_swiftui_01.jpg
---
Can you imagine, UIKit is 11 years old! Ever since the release of the iOS SDK in 2008 we were building our apps with it. And throughout this time the developers were in a relentless search for the best architecture to use for their apps. It all started with **MVC**, but later we witnessed the rise of **MVP**, **MVVM**, **VIPER**, **RIBs**, and **VIP**.

But something has happened recently. This "something" is so significant, that the majority of the architectural patterns used for iOS will soon become history.

I'm talking about SwiftUI. It's not going anywhere. Like it or not, this is the future of iOS development. And it's a game-changer in terms of the challenges we face when designing the architecture.

# What are the conceptual changes?

UIKit was an object-oriented, event-driven framework. We could reference each view in the hierarchy, update it's appearance when the view is loaded or as a reaction on an event (a touch-down on the button or a new data becoming available for display in UITableView). We used callbacks, delegates, target-actions for handling these events.

Now, it is all gone. SwiftUI is a declarative, state-driven framework. Event handling happens solely with closure callbacks, which offers higher cohesion for the business logic behind handling the input. So it has become easier to extract the business logic to an external module than it was with MVC: fixing "massive view" is a breeze compared to refactoring the "massive view controller".

The view is dehydrated to be a programming function. You provide it with input (the state) - it draws the output. And there is no way to directly mutate the view or dynamically change the input sources once the view is created - it all has to be declared in the construction of the view.

<div style="max-width:600px; display: block; margin-left: auto; margin-right: auto;"><img src="{{ site.url }}/assets/img/clean_swiftui_02.jpg"></div>

# MVVM is the new standard architecture

SwiftUI comes with MVVM built-in. The attributes like `@Published` or `@State` allow us to bind the view with the state, while `@ObservableObject` can take the role of the ViewModel for encapsulation of the business logic and providing the bindable data.

MVVM is, in fact, the new standard architecture Apple has declared as a successor of MVC for SwiftUI:

<div style="max-width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="{{ site.url }}/assets/img/clean_swiftui_03.jpg"></div>

> And well, you don't need a ViewController anymore.

So if you choose to design the architecture for your SwiftUI app in an MVVM style, you'd come up with something like this:

```swift
// Model
struct Country {
    let name: String
}

// View
struct CountriesList: View {
    
    @ObservedObject var viewModel: ViewModel
    
    var body: some View {
        List(viewModel.countries) { country in
            Text(country.name)
        }.onAppear {
            self.viewModel.loadCountries()
        }
    }
}

// ViewModel
extension CountriesList {
    class ViewModel: ObservableObject {
        @Published private(set) var countries: [Country] = []
        
        private let service: WebService
        
        func loadCountries() {
            service.getCountries { [weak self] result in
                self?.countries = result.value ?? []
            }
        }
    }
}
```

When the View appears on the screen, the `onAppear` callback triggers the `viewModel.loadCountries()`, which ultimately pushes the updated list of countries through `@Published` variable. As simple as that.

# Router is history

Router (aka Coordinator) was an essential part of VIPER, RIBs and MVVM-R architectures. Allocation of a separate module for screen navigation was well justified in UIKit apps – the direct routing from one ViewController to another led to their tight coupling, not to mention the coding hell of deep linking to a screen deeply inside the ViewController's hierarchy.

Well, SwiftUI made Router needless.

Every view that alters the displayed hierarchy, be that `NavigationView`, `TabView` or `.sheet()`, now uses `Binding` to control what's displayed.

`Binding` is an "unpossessed" form of a state variable - you can read and write it, but the factual value belongs to another module.

When the user selects a tab in the `TabView`, you don't get a callback. You simply cannot. What happens instead, is that the `TabView` unilaterally changes the value through `Binding` to "displayedTab = .userFavorites".

The programmer can also assign a value to that `Binding` at any time - and the `TabView` will obey immediately.

The programmatic navigation in SwiftUI is fully controlled by the state through `Bindings`. I dedicated a [separate article]({{ site.url }}/swiftui-deep-linking/) to this problem.

# Are VIPER, RIBs and VIP applicable for SwiftUI?

There are a lot of great ideas and concepts we can borrow from these architectures, but ultimately the canonical implementation of either one doesn't make sense for the SwiftUI app.

First, as you already know, there is no more practical need to have a `Router`.

Secondly, the completely new design of the data flow in SwiftUI coupled with native support of view-state bindings shrank the required setup code to the degree that `Presenter` becomes a goofy entity doing nothing useful. Along with the decreased number of modules in the pattern, we figure out that we don’t need `Builder` either.

So basically, the whole pattern just falls apart, as the problems it aimed to solve don't exist anymore.

There are [attempts](https://theswiftdev.com/2019/09/18/how-to-build-swiftui-apps-using-viper/) to stick with the beloved architectures no matter what, but please, don’t.

# SwiftUI is conceptually ELM Architecture

Just watch a couple minutes from this talk "MCE 2017: Yasuhiro Inami, Elm Architecture in Swift" from 28:26

<div style="position: relative; width: 100%; padding-bottom: 56.25%; height: 0;">
<iframe width="560" height="315" style="position: absolute; top:0; left: 0; width: 100%; height: 100%;" src="https://www.youtube.com/embed/U805TqsDIV8?start=1706" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

That guy had a WORKING prototype of SwiftUI in 2017! Of course, since it was backed by UIKit it had terrible performance, but still!

Does it feel like we're on a reality show where SwiftUI, a half-orphan kid, has just got to know who his father is?

Anyways, what interests us is whether we can use any other ELM concepts for making our SwiftUI apps better.

I followed the [ELM Architecture](https://guide.elm-lang.org/architecture/) description on the ELM language's web site and... found nothing new. SwiftUI is based on the same essences as ELM:

> * Model — the state of your application
> * View — a way to turn your state into HTML
> * Update — a way to update your state based on messages

We saw this somewhere, didn't we?

<div style="max-width:600px; display: block; margin-left: auto; margin-right: auto;"><img src="{{ site.url }}/assets/img/clean_swiftui_04.jpg"></div>

We already have the `Model`, the `View` gets generated automatically from the `Model`, the only thing we can tweak is the way `Update` in delivered. We can go **REDUX** way and use the `Command` pattern for state mutation instead of letting SwiftUI’s views and other modules write to the state directly.
Although I preferred using REDUX in my previous UIKit projects (ReSwift ❤), it’s questionable whether it’s needed for a SwiftUI app — the data flows are already under control and are easily traceable.

# Clean Architecture. Again

Let's not play the architect and just refer to the [Uncle Bob's Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html), the progenitor of VIP.

> By separating the software into layers, and conforming to The Dependency Rule, you will create a system that is intrinsically testable, with all the benefits that imply.

Clean Architecture is quite liberal about the number of layers we should introduce because this depends on the application domain.

But in the most common scenario for a mobile app we'll need to have three layers:

* Presentation layer
* Business Logic layer
* Data Access layer

So if we distilled the requirements of the Clean Architecture through the peculiarity of SwiftUI, we'd come up with something like this:

<div style="max-width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://github.com/nalexn/blob_files/blob/master/images/swiftui_arc_001.png?raw=true" alt="Diagram"/></div>

There is a [demo project](https://github.com/nalexn/clean-architecture-swiftui) I've created to illustrate the use of this pattern. The app talks to the [restcountries.eu](https://restcountries.eu/) REST API to show the list of countries and details about them.

# AppState

`AppState` is the only entity in the pattern that requires to be an object, specifically, an `ObservableObject`.

Works as the single source of truth and keeps the state for the entire app, including user's data, authentication tokens, screen navigation state (selected tabs, presented sheets) and system state (is active / is backgrounded, etc.)

AppState knows nothing about any other layer and does not have any business logic.

An example of the [AppState](https://github.com/nalexn/clean-architecture-swiftui/blob/master/CountriesSwiftUI/Injected/AppState.swift) from the Countries demo project:

```swift
class AppState: ObservableObject, Equatable {
    @Published var userData = UserData()
    @Published var routing = ViewRouting()
    @Published var system = System()
}
```

# View

This is the usual SwiftUI's view. It may be stateless or have local `@State` variables.

No other layers know about the View layer existence, so there is no need to hide it behind a protocol.

When the view is instantiated, it receives
`AppState` and `Interactor` through the SwiftUI's standard dependency injection of a variable attributed with `@Environment`, `@EnvironmentObject` or `@ObservedObject`.

Side effects are triggered by the user's actions (such as a tap on a button) or view lifecycle event `onAppear` and are forwarded to the `Interactor`.

```swift
struct CountriesList: View {
    
    @EnvironmentObject var appState: AppState
    @Environment(\.interactors) var interactors: InteractorsContainer
    
    var body: some View {
        ...
        .onAppear {
            self.interactors.countriesInteractor.loadCountries()
        }
    }
}
```

# Interactor

`Interactor` encapsulates the business logic for the specific `View` or a group of views. Together with the `AppState` forms the Business Logic layer, that's fully independent of the presentation and the external resources.

It is fully stateless and only refers to the `AppState` object, injected as a constructor parameter.

Interactors should be "facaded" with a protocol so that the `View` could talk to a mocked `Interactor` in tests.

Interactors receive requests to perform work, such as obtaining data from an external source or making computations, but they never return data back directly, such as in a closure.

Instead, they forward the result to the `AppState` or a `Binding` provided by the View.

The `Binding` is used when the result of work (the data) is owned locally by one View and does not belong to the central `AppState`, that is, it doesn't need to be persisted or shared with other screens of the app.

[CountriesInteractor](https://github.com/nalexn/clean-architecture-swiftui/blob/master/CountriesSwiftUI/Interactors/CountriesInteractor.swift) from the demo project:

```swift
protocol CountriesInteractor {
    func loadCountries()
    func load(countryDetails: Binding<Loadable<Country.Details>>, country: Country)
}

struct RealCountriesInteractor: CountriesInteractor {
    
    let webRepository: CountriesWebRepository
    let appState: AppState
    
    init(webRepository: CountriesWebRepository, appState: AppState) {
        self.webRepository = webRepository
        self.appState = appState
    }

    func loadCountries() {
        appState.userData.countries = .isLoading(last: appState.userData.countries.value)
        weak var weakAppState = appState
        _ = webRepository.loadCountries()
            .sinkToLoadable { weakAppState?.userData.countries = $0 }
    }

    func load(countryDetails: Binding<Loadable<Country.Details>>, country: Country) {
        countryDetails.wrappedValue = .isLoading(last: countryDetails.wrappedValue.value)
        _ = webRepository.loadCountryDetails(country: country)
            .sinkToLoadable { countryDetails.wrappedValue = $0 }
    }
}
```

# Repository

`Repository` is an abstract gateway for reading / writing data.
Provides access to a single data service, be that a web server or a local database.

For example, if the app is using its backend, Google Maps APIs and writes something to a local database, there will be three Repositories: two for different web API providers and one for database IO operations.

The repository is also stateless, doesn't have write access to the `AppState`, contains only the logic related to working with the data. It knows nothing about `View` or `Interactor`.

The factual Repository should be hidden behind a protocol so that the `Interactor` could talk to a mocked `Repository` in tests.

[CountriesWebRepository](https://github.com/nalexn/clean-architecture-swiftui/blob/master/CountriesSwiftUI/Repositories/CountriesWebRepository.swift) from the demo project:

```swift

protocol CountriesWebRepository: WebRepository {
    func loadCountries() -> AnyPublisher<[Country], Error>
    func loadCountryDetails(country: Country) -> AnyPublisher<Country.Details.Intermediate, Error>
}

struct RealCountriesWebRepository: CountriesWebRepository {
    
    let session: URLSession
    let baseURL: String
    let bgQueue = DispatchQueue(label: "bg_parse_queue")
    
    init(session: URLSession, baseURL: String) {
        self.session = session
        self.baseURL = baseURL
    }
    
    func loadCountries() -> AnyPublisher<[Country], Error> {
        return call(endpoint: API.allCountries)
    }

    func loadCountryDetails(country: Country) -> AnyPublisher<Country.Details, Error> {
        return call(endpoint: API.countryDetails(country))
    }
}

extension RealCountriesWebRepository {
    enum API: APICall {
        case allCountries
        case countryDetails(Country)
        
        var path: String { ... }
        var httpMethod: String { ... }
        var headers: [String: String]? { ... }
    }
}
```

Since WebRepository takes URLSession as a constructor parameter, it is very easy to test it by [mocking the networking calls](https://github.com/nalexn/clean-architecture-swiftui/blob/master/UnitTests/Mocks/RequestMocking.swift) with a custom `URLProtocol`

# Final thoughts

[The demo project](https://github.com/nalexn/clean-architecture-swiftui) now has **97% test coverage**, all thanks to the Clean Architecture's "dependency rule" and segregation of the app on multiple layers.

<div style="max-width:800px; display: block; margin-left: auto; margin-right: auto;"><img src="https://github.com/nalexn/blob_files/blob/master/images/countries_preview.png?raw=true" alt="Diagram"/></div>

I'm planning a separate article for a detailed explanation of the technical design desitions made during its development, as well as numerous "gotchas!" I had with SwiftUI. Make sure to follow me on [Twitter](https://twitter.com/nallexn)!

Shall we assign a name to this architecture? Something like "VIRS"? Propose your variant in the comments!