---
layout: post
title:  Factory-based serialisation in Go
---

I have an interface called Transform which can be applied to some data.

~~~ go
type Transform interface {
    Rate() int
    Apply(x []float64) ([]float64, error)
}
~~~

I have a number of classes which satisfy this interface. For example

~~~ go
type Pow struct{ Expt float64 }

func (t Pow) Rate() int { return 1 }

func (t Pow) Apply(x []float64) ([]float64, error) {
    y := make([]float64, len(x))
    for i, xi := range x {
        y[i] = math.Pow(xi, t.Expt)
    }
    return y, nil
}
~~~

~~~ go
type Subsample struct{ Stride int }

func (t Pow) Rate() int { return t.Stride }

func (t Pow) Apply(x []float64) ([]float64, error) {
    y := make([]float64, len(x)/t.Stride)
    for i := range y {
        y[i] = x[i*t.Stride]
    }
    return y, nil
}
~~~

and I want to load a list of Transforms from a file.
We can define a factory like so

~~~ go
type Factory map[string]CreateFunc

type CreateFunc func() Transform

func (fact Factory) Register(name string, create CreateFunc) error {
    if _, taken := fact[name]; taken {
        return errors.New("name taken")
    }
    fact[name] = create
}

func (fact Factory) Create(name string) (Transform, error) {
    if _, found := fact[name]; !found {
        return errors.New("name not found")
    }
    return fact[name](), nil
}
~~~

and then call

~~~ go
var factory Factory

func init() {
    factory.Register("pow", func() Transform { return new(Pow) })
    factory.Register("subsample", func() Transform { return new(Subsample) })
}
~~~
