[<img src="https://img.shields.io/gitter/room/help-me-mom/@kollorg/quaerat-dolorem-soluta" alt="chat on gitter" width="90" height="20" />](https://gitter.im/@kollorg/quaerat-dolorem-soluta/community)
[<img src="https://img.shields.io/npm/v/@kollorg/quaerat-dolorem-soluta" alt="npm version" width="88" height="20" />](https://www.npmjs.com/package/@kollorg/quaerat-dolorem-soluta)
[<img src="https://img.shields.io/circleci/build/github/help-me-mom/@kollorg/quaerat-dolorem-soluta/master" alt="build status" width="88" height="20" />](https://app.circleci.com/pipelines/github/help-me-mom/@kollorg/quaerat-dolorem-soluta?branch=master)
[<img src="https://img.shields.io/coveralls/github/help-me-mom/@kollorg/quaerat-dolorem-soluta/master" alt="coverage status" width="104" height="20" />](https://coveralls.io/github/help-me-mom/@kollorg/quaerat-dolorem-soluta?branch=master)

# Mock components, services and more out of annoying dependencies for simplification of Angular testing

`@kollorg/quaerat-dolorem-soluta` facilitates **Angular testing** and helps to:

- **mock Components**, Directives, Pipes, Modules, Services and Tokens
- reduce boilerplate in tests
- access declarations via simple interface

The current version of the library **has been tested** and **can be used** with:

| Angular | @kollorg/quaerat-dolorem-soluta | Jasmine | Jest | Ivy |
| ------: | :------: | :-----: | :--: | :-: |
|      18 |  latest  |   yes   | yes  | yes |
|      17 |  latest  |   yes   | yes  | yes |
|      16 |  latest  |   yes   | yes  | yes |
|      15 |  latest  |   yes   | yes  | yes |
|      14 |  latest  |   yes   | yes  | yes |
|      13 |  latest  |   yes   | yes  | yes |
|      12 |  latest  |   yes   | yes  | yes |
|      11 |  latest  |   yes   | yes  | yes |
|      10 |  latest  |   yes   | yes  | yes |
|       9 |  latest  |   yes   | yes  | yes |
|       8 |  latest  |   yes   | yes  |     |
|       7 |  latest  |   yes   | yes  |     |
|       6 |  latest  |   yes   | yes  |     |
|       5 |  latest  |   yes   | yes  |     |

## Important links

- **[Documentation with examples of Angular testing](https://@kollorg/quaerat-dolorem-soluta.sudo.eu)**
- [CHANGELOG](https://github.com/kollorg/quaerat-dolorem-soluta/blob/master/CHANGELOG.md)
- [GitHub repo](https://github.com/kollorg/quaerat-dolorem-soluta)
- [NPM package](https://www.npmjs.com/package/@kollorg/quaerat-dolorem-soluta)

* Live [example on CodeSandbox](https://codesandbox.io/p/sandbox/github/help-me-mom/@kollorg/quaerat-dolorem-soluta-sandbox/tree/master/?file=/src/test.spec.ts)
* Live [example on StackBlitz](https://stackblitz.com/github/help-me-mom/@kollorg/quaerat-dolorem-soluta-sandbox?file=src/test.spec.ts)

- [start a discussion on GitHub](https://github.com/kollorg/quaerat-dolorem-soluta/discussions/new/choose)
- **[ask a question on Stackoverflow](https://stackoverflow.com/questions/ask?tags=@kollorg/quaerat-dolorem-soluta%20angular%20testing%20mocking)**
- [report an issue on GitHub](https://github.com/kollorg/quaerat-dolorem-soluta/issues)
- [chat on gitter](https://gitter.im/@kollorg/quaerat-dolorem-soluta/community)

## Very short introduction

Global configuration for mocks in `src/test.ts`.
In case of jest, `src/setup-jest.ts` / `src/test-setup.ts` should be used.

```ts title="src/test.ts"
// All methods in mock declarations and providers
// will be automatically spied on their creation.
// https://@kollorg/quaerat-dolorem-soluta.sudo.eu/extra/auto-spy
ngMocks.autoSpy('jasmine'); // or jest

// ngMocks.defaultMock helps to customize mocks
// globally. Therefore, we can avoid copy-pasting
// among tests.
// https://@kollorg/quaerat-dolorem-soluta.sudo.eu/api/ngMocks/defaultMock
ngMocks.defaultMock(AuthService, () => ({
  isLoggedIn$: EMPTY,
  currentUser$: EMPTY,
}));
```

An example of a spec for a profile edit component.

```ts title="src/profile.component.spec.ts"
// Let's imagine that there is a ProfileComponent
// and it has 3 text fields: email, firstName,
// lastName, and a user can edit them.
// In the following test suite, we would like to
// cover behavior of the component.
describe('profile:builder', () => {
  // Helps to reset customizations after each test.
  // Alternatively, you can enable
  // automatic resetting in test.ts.
  MockInstance.scope();

  // Let's configure TestBed via MockBuilder.
  // The code below says to mock everything in
  // ProfileModule except ProfileComponent and
  // ReactiveFormsModule.
  beforeEach(() => {
    // The result of MockBuilder should be returned.
    // https://@kollorg/quaerat-dolorem-soluta.sudo.eu/api/MockBuilder
    return MockBuilder(
      ProfileComponent,
      ProfileModule,
    ).keep(ReactiveFormsModule);
    // // or old fashion way
    // return TestBed.configureTestingModule({
    //   imports: [
    //     MockModule(SharedModule), // mock
    //     ReactiveFormsModule, // real
    //   ],
    //   declarations: [
    //     ProfileComponent, // real
    //     MockPipe(CurrencyPipe), // mock
    //     MockDirective(HoverDirective), // mock
    //   ],
    //   providers: [
    //     MockProvider(AuthService), // mock
    //   ],
    // }).compileComponents();
  });

  // A test to ensure that ProfileComponent
  // can be created.
  it('should be created', () => {
    // MockRender is an advanced version of
    // TestBed.createComponent.
    // It respects all lifecycle hooks,
    // onPush change detection, and creates a
    // wrapper component with a template like
    // <app-root ...allInputs></profile>
    // and renders it.
    // It also respects all lifecycle hooks.
    // https://@kollorg/quaerat-dolorem-soluta.sudo.eu/api/MockRender
    const fixture = MockRender(ProfileComponent);

    expect(
      fixture.point.componentInstance,
    ).toEqual(assertion.any(ProfileComponent));
  });

  // A test to ensure that the component listens
  // on ctrl+s hotkey.
  it('saves on ctrl+s hot key', () => {
    // A fake profile.
    const profile = {
      email: 'test2@email.com',
      firstName: 'testFirst2',
      lastName: 'testLast2',
    };

    // A spy to track save calls.
    // MockInstance helps to configure mock
    // providers, declarations and modules
    // before their initialization and usage.
    // https://@kollorg/quaerat-dolorem-soluta.sudo.eu/api/MockInstance
    const spySave = MockInstance(
      StorageService,
      'save',
      jasmine.createSpy(), // or jest.fn()
    );

    // Renders <profile [profile]="params.profile">
    // </profile>.
    // https://@kollorg/quaerat-dolorem-soluta.sudo.eu/api/MockRender
    const { point } = MockRender(
      ProfileComponent,
      { profile }, // bindings
    );

    // Let's change the value of the form control
    // for email addresses with a random value.
    // ngMocks.change finds a related control
    // value accessor and updates it properly.
    // https://@kollorg/quaerat-dolorem-soluta.sudo.eu/api/ngMocks/change
    ngMocks.change(
      '[name=email]', // css selector
      'test3@em.ail', // an email address
    );

    // Let's ensure that nothing has been called.
    expect(spySave).not.toHaveBeenCalled();

    // Let's assume that there is a host listener
    // for a keyboard combination of ctrl+s,
    // and we want to trigger it.
    // ngMocks.trigger helps to emit events via
    // simple interface.
    // https://@kollorg/quaerat-dolorem-soluta.sudo.eu/api/ngMocks/trigger
    ngMocks.trigger(point, 'keyup.control.s');

    // The spy should be called with the user
    // and the random email address.
    expect(spySave).toHaveBeenCalledWith({
      email: 'test3@em.ail',
      firstName: profile.firstName,
      lastName: profile.lastName,
    });
  });
});
```

Profit.

## Extra

If you like `@kollorg/quaerat-dolorem-soluta`, please support it:

- [with a star on GitHub](https://github.com/kollorg/quaerat-dolorem-soluta)
- [with a tweet](https://twitter.com/intent/tweet?text=Check%20@kollorg/quaerat-dolorem-soluta%20package%20%23angular%20%23testing%20%23mocking&url=https%3A%2F%2Fgithub.com%2Fhelp-me-mom%2F@kollorg/quaerat-dolorem-soluta)

Thank you!

P.S. Feel free to [contact us](https://@kollorg/quaerat-dolorem-soluta.sudo.eu/need-help) if you need help.
