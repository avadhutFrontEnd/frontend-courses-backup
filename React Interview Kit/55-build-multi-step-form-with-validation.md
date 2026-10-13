---
title: "Build: Multi-Step Form with Validation"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-multi-step-form-with-validation
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Multi-Step Form with Validation

*2 min read · Updated 23 Apr 2026 Topic 55 of 62 Bookmark Mark done Problem · Topic 55 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-multi-step-form-with-validation](https://www.geekyfrontend.in/learn/react-interview-kit/build-multi-step-form-with-validation)

---

**Problem:** Build a 3-step registration form (personal info, address, review and submit) with field validation.

**What This Tests:** Multi-step state management, form validation, step navigation, controlled inputs.

jsx

jsx

```
function MultiStepForm() {
  const [step, setStep] = useState(1);
  const [formData, setFormData] = useState({
    name: '', email: '', password: '',
    street: '', city: '', zip: '',
  });
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
    if (errors[name]) setErrors(prev => ({ ...prev, [name]: '' }));
  };

  const validateStep = () => {
    const newErrors = {};
    if (step === 1) {
      if (!formData.name.trim()) newErrors.name = 'Name is required';
      if (!formData.email.includes('@')) newErrors.email = 'Valid email required';
      if (formData.password.length < 6) newErrors.password = 'Min 6 characters';
    }
    if (step === 2) {
      if (!formData.street.trim()) newErrors.street = 'Street is required';
      if (!formData.city.trim()) newErrors.city = 'City is required';
      if (!formData.zip.trim()) newErrors.zip = 'ZIP is required';
    }
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const nextStep = () => { if (validateStep()) setStep(s => s + 1); };
  const prevStep = () => setStep(s => s - 1);

  const handleSubmit = () => {
    alert('Submitted: ' + JSON.stringify(formData, null, 2));
  };

  const Field = ({ name, label, type = 'text' }) => (
    <div>
      <label>{label}</label>
      <input name={name} type={type} value={formData[name]} onChange={handleChange} />
      {errors[name] && <span style={{ color: 'red' }}>{errors[name]}</span>}
    </div>
  );

  return (
    <div>
      <p>Step {step} of 3</p>

      {step === 1 && (
        <div>
          <h3>Personal Info</h3>
          <Field name="name" label="Full Name" />
          <Field name="email" label="Email" type="email" />
          <Field name="password" label="Password" type="password" />
        </div>
      )}

      {step === 2 && (
        <div>
          <h3>Address</h3>
          <Field name="street" label="Street" />
          <Field name="city" label="City" />
          <Field name="zip" label="ZIP Code" />
        </div>
      )}

      {step === 3 && (
        <div>
          <h3>Review</h3>
          <p>Name: {formData.name}</p>
          <p>Email: {formData.email}</p>
          <p>Address: {formData.street}, {formData.city} {formData.zip}</p>
        </div>
      )}

      <div>
        {step > 1 && <button onClick={prevStep}>Back</button>}
        {step < 3 ? (
          <button onClick={nextStep}>Next</button>
        ) : (
          <button onClick={handleSubmit}>Submit</button>
        )}
      </div>
    </div>
  );
}
```

**Key Concepts:** Step-based navigation, per-step validation, shared form state across steps, clearing errors on input change, review step, internal reusable Field component.

---

## Learn More

- [React Hook Form react-hook-form.com](https://react-hook-form.com)
- [Sharing State Between Components react.dev](https://react.dev/learn/sharing-state-between-components)
- [Create a multi-step form in React YouTube](https://www.youtube.com/watch?v=bdeQMFdf-hE)
- [Multi-step form with validation YouTube](https://www.youtube.com/watch?v=FqThW74O0fQ)
- [Multi-step form with react-hook-form YouTube](https://www.youtube.com/watch?v=Wlz2cy33bMU)
