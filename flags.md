# command line flags

### Enable features + parameters

Enabling or disabling features via the command line:

#### base::Feature

```
--enable-features=DelayAsyncScriptExecution
```

#### RuntimeEnabledFetaures

```
--enable-blink-features=DelayAsyncScriptExecutionUntilFinishedParsing"
```

#### Features with parameters:

See https://bugs.chromium.org/p/chromium/issues/detail?id=1068052 for context.

```
--enable-features="DelayAsyncScriptExecution<DummyTrial" --force-fieldtrials=DummyTrial/Group1 --force-fieldtrial-params=DummyTrial.Group1:delay_type/"use_optimization_guide"
```

... or ...

```
--enable-features="DelayAsyncScriptExecution:param1/100/param2/use_optimization_guide"
```
